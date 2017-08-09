---
title: CORS AJAX
date: 2017-08-04 11:43:12
tags:
- CORS
- AJAX
- Apache 
categories:
- Web server
---
For security reasons, browsers restrict cross-origin HTTP requests initiated from within scripts (e.g. AJAX request). For below scenario, we need to modify the HTTP response headers:

https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS

{% img "https://docs.google.com/drawings/d/15RJbrTnTn1dATx_Eujw73JyrifygW1Xv5PYHIjD0IfY/pub?w=417&amp;h=279" %}
<!-- more -->
{% codeblock httpd.conf lang:apache https://httpd.apache.org/docs/2.4/configuring.html Apache-httpd.conf %}
<Location "/service/" >
  SetEnvIf Origin "http(s)?://(b|c)?(.domain.com)$" AccessControlAllowOrigin=$0
  Header add Access-Control-Allow-Origin %{AccessControlAllowOrigin}e env=AccessControlAllowOrigin
  Header set Access-Control-Allow-Credentials true
</Location>
{% endcodeblock %}

 The above example will allow requests to services under */service/* from http or https request from *b.domain* and *c.domain*. If we want the request to aware of the HTTP cookies, then we need add `Header set Access-Control-Allow-Credentials true`.

{% codeblock "Sample AJAX program" lang:javascript  http://api.jquery.com/jquery.ajax/ jQuery.ajax() %}
<script language="Javascript">
$.ajax({
  type: "GET",
  url: "https://a.domain.com/service/test.jsp",
  xhrFields: {
      withCredentials: true
   },
}).done(function(data) {
    console.log(data);
    alert( "success" );
  }).fail(function() {
    alert( "error" );
  });

</script>
{% endcodeblock %}

`withCredentials: true` is used to mark `XMLHttpRequest` to aware of HTTP cookies (e.g. session id).

