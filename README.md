## Tutorial on how to block Nebula and other web Proxies using Fortiguard
-------------------------------------------------------------------------------------------------------------------------------------
**Enhancing Control and Blocking of Personally Crafted Web Proxies on FortiGate**

**This comprehensive guide provides FortiGate administrators with a step-by-step approach to effectively control and block personally crafted web proxies. By utilizing the content filter feature, a scoring system based on specific page elements can be implemented, allowing administrators to take desired actions accordingly.**

**Moreover, this guide can be easily customized to block other services by modifying keywords or components of the target page to suit specific needs.**

**FortiGuard web filter categories may categorize customized tools that employ personal domains as 'Unrated' or uncategorized. While categorization requests can be submitted or custom categories can be created to address this, it is important to note that these options rely on manual observation and subsequent actions.**

**To automate the blocking process, the content filtering feature offers an automated score system that can block pages based on the presence of a defined number of specific words. However, caution should be exercised when configuring matching criteria to avoid false positives that may result from poorly constructed criteria.**

_**For more detailed information on the web content filter and its functionalities, please refer to the official Fortinet [documentation](https://docs.fortinet.com/document/fortigate/7.4.0/administration-guide/725397/web-content-filter.)**_
-------------------------------------------------------------------------------------------------------------------------------------
## SSL Deep Inspection is required on HTTPS websites to ensure the content filter has access to the encrypted page and the full page contents.
-------------------------------------------------------------------------------------------------------------------------------------
### Step 1. Make sure the web filter profile is in proxy [mode](https://community.fortinet.com/t5/image/serverpage/image-id/23410iD70767212E770D6C/image-size/large/is-moderation-mode/true?v=v2&px=999) (Image Included)
![feature_set](https://github.com/K12SystemAdmin/Blocking-Web-Proxies/assets/133791743/e612bfd3-2377-4296-a844-bb2e0915b483)


### Step 2. Enable the web content filter on a web filter [profile](https://community.fortinet.com/t5/image/serverpage/image-id/23411iD2E9BA36FF177F48/image-size/large/is-moderation-mode/true?v=v2&px=999)
![content_filter_enable](https://github.com/K12SystemAdmin/Blocking-Web-Proxies/assets/133791743/1c4173e3-5748-4480-a35b-cf06ca4f509b)


### Step 3. Add the following entries as wildcards and set the action to 'Block' for each. The list of words below should be updated as [Needed](https://community.fortinet.com/t5/image/serverpage/image-id/23412iC8806D8E9F326FEC/image-size/large/is-moderation-mode/true?v=v2&px=999)
![nebula_contents](https://github.com/K12SystemAdmin/Blocking-Web-Proxies/assets/133791743/4c406ab7-0df9-4749-9abe-3eb4adca36f5)


### Step 4.  Link the web filter profile to a firewall policy and set the policy to proxy mode as [well](https://community.fortinet.com/t5/image/serverpage/image-id/23413i5B351B8594489C66/image-size/large/is-moderation-mode/true?v=v2&px=999)
![nebula_fw_policy](https://github.com/K12SystemAdmin/Blocking-Web-Proxies/assets/133791743/15872bab-e1d5-4736-b2db-fc0574afe6db)

-------------------------------------------------------------------------------------------------------------------------------------
**CLI Script**
```
  config webfilter content
    edit 1
      set name "block_nebulaweb"
      config entries
         edit "*nebulaclock.js"
            set status enable
         next
         edit "*nebulamain.js"
            set status enable
         next
         edit "*stealthEngine.js"
            set status enable
         next
         edit "NebulaWeb"
            set status enable
         next
         edit "\"Nebula Services\""
            set status enable
         next
         edit "unblocker"
            set status enable
        next
      end
   next
end
 
  config webfilter profile
    edit "block_nebula"
      set feature-set proxy
      config web
         set bword-threshold 20
        set bword-table 1
      end
   end
end
```
_*Code may or may not be correctly indented so its best if you go to the Fortiguard [Post](https://community.fortinet.com/t5/FortiGate/Technical-Tip-How-to-block-Nebula-Web-Proxy-and-custom-web/ta-p/258816)*_

This is what the code is supposed to look [like](https://imgur.com/a/Rp1mPOD)

![8OkTtPm](https://github.com/K12SystemAdmin/Blocking-Web-Proxies/assets/133791743/64b4762f-7b8d-497d-aeed-fce78d30d1b3)

-------------------------------------------------------------------------------------------------------------------------------------
### When testing, it should look something like this![successful_block](https://github.com/K12SystemAdmin/Blocking-Web-Proxies/assets/133791743/31a273eb-e39b-4bb6-9517-e67c280235d4)
-------------------------------------------------------------------------------------------------------------------------------------
# Important Things To Note

The web filter logs should log other portions of the page that are allowed until the content filter score triggers.

Notice the above page was initially allowed because it is part of the Information Technology category, which is one category that was set to be allowed. The newest log entry shows the final result where the page was blocked:

![webfilter_log](https://github.com/K12SystemAdmin/Blocking-Web-Proxies/assets/133791743/177ed0b2-1082-4cd2-860e-e6be1d6b7197)

The Log details show the page was blocked because of 'Banned Words'. Note that two items were triggered. Upon comparing with the CLI web filter settings, it can be seen that the content filter is set with a threshold of 20. Each match sums up 10 to the score, so with the above settings, the page is only blocked two times the items are matched. This decreases the chance of false positives. 

![webfilter_log_details](https://github.com/K12SystemAdmin/Blocking-Web-Proxies/assets/133791743/c0cf61e8-86fd-457f-aa42-fab5c43600ff)
-------------------------------------------------------------------------------------------------------------------------------------

Huge thanks to the people working in Fortiguard and with providing information for a fix on this! 

_**All information can be found on their website, everything here was taken from their website**_

![download-removebg-preview (1)](https://github.com/K12SystemAdmin/Blocking-Web-Proxies/assets/133791743/97a49e9c-eaa6-4aab-af14-a9ae6e7dcafb)
