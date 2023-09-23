Title: 'Alert' Filter for Logstash
Date: 2014-07-15 20:15
Author: Jacob Morgan
Category: Operations
Tags: logstash, elasticsearch, ruby
Slug: logstash-filter-alert

I've attempted to write a filter for logstash. It's called 'Alert', i've submitted a pull request on github for it [here](https://github.com/elasticsearch/logstash-contrib/pull/85). It is a work in progress, but the basic functionality I wanted appears to work. The initial requirements were:

*  Generate a single alert when something goes wrong. Don't spam.
*  Be able to alert on both volume of a particular message or lack thereof
*  If alerting on lack thereof, alert even if there was 0 messages during the period


I started with the throttle filter but felt the use case I wanted to use was a bit different than that filter was intended to do. I used it's code as a base for this filter. Disclaimer: This is my first attempt at Ruby, so I am sure it is quite rough code wise. :D

For all the examples below we will use the typical httpd access logs we all know and love.

    :::bash
    input {
      file {
        path => "/var/log/httpd/access_log"
        type => "httpd"
      }
    }

    filter {
      grok {
        match => ["message", "%{COMBINEDAPACHELOG}" ]
      }
      date {
      #14/Jun/2014:23:31:16 -0400
        match => ["timestamp", "dd/MMM/YYYY:HH:mm:ss Z"]
      }


*  Generate an alert if there are more than 10 404s in a 5 minute period. We will use the [response] field created by %{COMBINEDAPACHELOG} and generate an alert for each message we see with an response code '404'. This is done using the max_threshold option.

 <!-- comment --> 

    :::bash
    #only call alert if response == 404
      if [response] == "404" {
        #generate an alert
        alert {
        max_threshold => 10
        max_threshold_msg => '404 Issues'
        period => 300
        key => "404Alert"
        }
     }

The above uses the key "404Alert" to track these events within the filter. For each trigger of this filter the count will be incremented. If 11 or more occurences are counted within 300 seconds a single event is created for the entire 300 second period.

*  Generate an alert if you do not see atleast 10 '202's in a 5 minute period. Using the same [response] field as above. You have two options when using the min_threshold option. Both options require the below config -- The actual event you are wanting to track:

 <!-- comment -->

    :::bash
    #only call alert if response == 202
      if [response] == "202" {
        #generate an alert
        alert {
        min_threshold => 10
        min_threshold_msg => 'Traffic to site below threshold'
        period => 300
        key => "202Alert"
        }
     }

This configuration will generate an alert if there is less than 10 events in a 5 minute period. However, the event can only be created when the alert filter is triggered. This means if you have 3 events in the first 5 minutes, then another 90 minutes later you will receive the alert at the 95 minute mark. 

To get around this limitation there is a 'heartbeat' flag one can use. This is the second option to min_threshold. Add the alert in either the root of filter(triggers on every event) or in another filter that has a high enough volume of events to ensure it will trigger. It would look like this:

    :::bash
      #Place in a 'high traffic' area. Either root of filter{} or somewhere to ensure the filter is regularly called
      alert {
      heartbeat => true
      min_threshold => 10
      min_threshold_msg => 'Traffic to site below threshold'
      period => 300
      key => "202Alert"
      }

For now you have to mirror your configuration in both alert filters, especially the key as that is how the filter tracks your events. The heartbeat flag causes the filter to only check two things:

*  Does an entry for this key exist? If not, make one and exit
*  If the key does exist, has my time expired? If so, evaluate the min_threshold. Alert if needed, delete key, exit.


The goal is to let you monitor the presence of certain logs to confirm stuff and things are working. If all the things die you still want to receive an alert! 
Once you've created your filters, you can then leverage the results in output{} . When a threshold alert is triggered a new event is created. The message and tag is configurable, by default the tag is 'alert_filter'.  Here is how you could send an email for the 404 alert above.

    :::bash
    output {
      if [alert_filter.key] == "202Alert"  {
        email {
          from => "logstash@dopey.io"
          subject => "%{message}"
          to => "dopey@dopey.io"
          via => "sendmail"
          body => "Min Threshold:%{min_threshold}\n\nNum of 202s:%{alert_filter.count}"
          options => { "location" => "/sbin/sendmail" }
        }
      }
    }  


Possible Options in logstash.conf:

    :::ruby
    #key - Unique identifier to track/count events
    config :key, :validate => :string,  :required => true
    #All fields created are formatted as "alert_filter.$tag" by default. Can change with this setting.
    #Also a tag will be created for the event with this value.
    config :filter_tag, :validate => :string, :default => "alert_filter", :required => false
  
    #Min threshold. Alert is created if # of events is < this number.
    config :min_threshold, :validate => :number, :default => -1, :required => false
    #Value that will be [message] for the created event. 
    config :min_threshold_msg, :validate => :string, :default => "Min Threshold Alert", :required => false
  
    #Max threshold. Alert is created if # of events is > this number.
    config :max_threshold, :validate => :number, :default => -1, :required => false
    #Value that will be [message] for the created event. 
    config :max_threshold_msg, :validate => :string, :default => "Max Threshold Alert", :required => false
    
    #Used with min_threshold to generate alerts independent on the event being tracked. 
    config :heartbeat, :validate => :boolean, :default => false
  
    #Time period to track, in seconds.
    config :period, :validate => :string, :required => true
 

Some future enhancements i'd like to do:

*  Not require duplication of configuration when leveraging heartbeat. 
*  Configure alarms only during certain times.
*  Improve performance, code is rough.
*  Make various tags created optional/configurable.


Hopefully someone else finds a use for this filter. We are just starting to implement this ourselves so I will likely be tweaking it as I find problem areas.  

