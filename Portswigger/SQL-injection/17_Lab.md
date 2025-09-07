* Title: SQL injection with filter bypass via XML encoding

* Goal: Log in as the administrator user.

* Vulnerability Location: After you intercept it, there is a portion that has XML code

* Payload Used:

  ``` UNION SELECT NULL``` ,    ``` UNION SELECT NULL, NULL``` ,

  ``` UNION SELECT username||'~'||password ```



* Steps:

  1. In the browser, choose one product and click on _View details_. After that, there is a _Check store_ button below. After you click it, that's where you intercept your network using Burp Suite.
 
  2. After you send that to the _Repeater_, there's an XML code below. And our playground is between _storeId_, where you see the store id of the product.
 
  3. You inject ``` UNION SELECT NULL``` after the number and click send. But the response says **Attack detected**. And that's when _Hackvertor_ come into play.
     _Hackvertor_ helps you to encode your payload so that it won't be detected.

  4. Now, select your payload (and the number) -> Right click on it -> Extensions -> Hackvertor -> Encode -> hex_entities. After this, send the  request again.
     (If you don't have _Hackvertor_ yet, In Burp Suite, go to Extender → BApp Store, search for Hackvertor, and click Install.)

  5. If you get a different response like "572 units null", it's working. When you add another _NULL_, you don't get the unit number anymore. That implies there is only one column.
 
  6. ``` UNION SELECT username||'~'||password ``` will get you the username along with password.
 
  * Takeaway: This write-up shows how encoding tricks can bypass naive filters and unlock SQL injection even in structured formats like XML.
