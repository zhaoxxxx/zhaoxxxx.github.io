---
layout: post
title:  "原生js获取本地ip地址"
categories: javascript
tags: javascript
author: 赵醒醒
---

* content
{:toc}

可以拿过来直接用，亲测有效，也是网上抄的，免得下次用还得百度
```
<script type="text/javascript">
function getUserIP(onNewIP) { //  onNewIp - your listener function for new IPs
      //compatibility for firefox and chrome
      var myPeerConnection = window.RTCPeerConnection || window.mozRTCPeerConnection || window.webkitRTCPeerConnection;
      var pc = new myPeerConnection({
         iceServers: []
     }),
     noop = function() {},
     localIPs = {},
     ipRegex = /([0-9]{1,3}(\.[0-9]{1,3}){3}|[a-f0-9]{1,4}(:[a-f0-9]{1,4}){7})/g,
     key;
     function iterateIP(ip) {
         if (!localIPs[ip]) onNewIP(ip);
         localIPs[ip] = true;
    }
      //create a bogus data channel
     pc.createDataChannel("");
     // create offer and set local description
     pc.createOffer().then(function(sdp) {
         sdp.sdp.split('\n').forEach(function(line) {
             if (line.indexOf('candidate') < 0) return;
             line.match(ipRegex).forEach(iterateIP);
         });
         pc.setLocalDescription(sdp, noop, noop);
     }).catch(function(reason) {
         // An error occurred, so handle the failure to connect
     });
     //sten for candidate events
     pc.onicecandidate = function(ice) {
         if (!ice || !ice.candidate || !ice.candidate.candidate || !ice.candidate.candidate.match(ipRegex)) return;
         ice.candidate.candidate.match(ipRegex).forEach(iterateIP);
     };
}
// Usage
getUserIP(function(ip){
     alert("Got IP! :" + ip);
});
</script>
```