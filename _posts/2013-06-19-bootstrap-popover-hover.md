---
layout: post
title: "bootstrap popover hover"
category:
tags: [bootstrap]
---
{% include JB/setup %}
Question: How can I hold Twitter Bootstrap Popover open until my mouse moves into it?

Answer:

    var timeoutObj;
    $("a[rel=popover]").popover({
        trigger: 'manual'
    }).on('mouseenter', function() {
        $(this).popover('show');
    }).on('mouseleave', function() {
        var ref = $(this);
        timeoutObj = setTimeout(function(){
          ref.popover('hide');
        }, 50);
    }).on('shown', function() {
        var ref = $(this);
        $('.popover').one('mouseover',function() {
          clearTimeout(timeoutObj);
          $(this).one('mouseleave',function() {ref.popover('hide');});
        })
    });

Live Demo:

<a class="jsbin-embed" href="http://jsbin.com/isuhum/2/embed?live">How can I hold Twitter Bootstrap Popover open until my mouse moves into it?</a>
