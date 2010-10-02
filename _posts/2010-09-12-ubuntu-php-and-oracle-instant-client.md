---
layout: article
uuid: cca54d32-5966-4885-8efe-becd77c3719a
title: Ubuntu, PHP, and Oracle Instant Client
categories: old
updated_at: 2010-09-12
---
A year or so ago I was using php (yuck!) and Oracle (yuck!) and I made a post about getting to work with ubuntu:

[Installing Oracle Instant Client 11.1, php_pdo, & oci8](http://ubuntuforums.org/showthread.php?t=1207665)

This is the script I found in my `~/` that prompted me to document this for future reference. This may be better or worse or the same as the one on the forum.

    <?php
    # USAGE: ORACLE_HOME=/opt/oracle/instantclient/ TNS_ADMIN=/opt/oracle/intstantclient/network/admin/ php $this_file
    # http://wiki.oracle.com/page/PHP+Oracle+FAQ
    # http://download-west.oracle.com/docs/cd/B12037_01/network.101/b10775/naming.htm#i498306
      echo 'Connecting using EZCONNECT dsn';
      $username = 'myusername';
      $password = 'mypassword';
      $host = 'db.example.org';
      $port = '1521';
      $tns_service_name = 'SOMENAME';
      $service_name = 'dbinstance.example.org';
      echo "\nConnecting using EZCONNECT dsn\n";
      oci_connect($username, $password, "//$host:$port/$service_name");
      echo "Connecting using TNSNAMES service name\n";
      oci_connect($username, $password, $tns_service_name);
      echo "\n";
    ?>
