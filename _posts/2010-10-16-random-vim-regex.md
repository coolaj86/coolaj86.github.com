---
layout: article
uuid: A4CE2290-CF56-4971-ACFC-4C4BCB4AEBBB
title: Random Vim Regex
name: random-vim-regex
created_at: 2010-10-16
updated_at: 2010-10-16
categories: unfinished
---

CampusBooks API
----

This will turn the tables at http://partners.campusbooks.com/api.php?pl_id=2496 into machine readable json

:%s/"/\\"/g
:%s/\(\w\+\)\s*\(.*\)/{\r  key: "\1",\r  description: "\2"\r},/

From this:

    isbn  The thirteen digit ISBN for this offer
    merchant_id A numeric merchant ID (Note, this value may be signed)
    merchant_name The Name of the merchant (looked up from the defined constants)
    merchant_image  URL of the merchant logo

To this:

    values = [
      {
        key: "isbn",
        description: "The thirteen digit ISBN for this offer"
      },
      {
        key: "merchant_id",
        description: "A numeric merchant ID (Note, this value may be signed)"
      },
      {
        key: "merchant_name",
        description: "The Name of the merchant (looked up from the defined constants)"
      },
      {
        key: "merchant_image",
        description: "URL of the merchant logo"
      }
    ]

Formatting XML in a JSON string
----

:%s/"/\\"/g
:%s/</\&lt;/g
:%s/>\n/>" +\r/
:%s/>/\&gt;/g
:%s/^/"/

From this:

    <merchant>
        <merchant_id>108</merchant_id>
        <merchant_image>http://www.campusbooks.com/images/markets/firstclassbooks.gif</merchant_iamge>
        <name>First Class Books</name>
        <notes>Free shipping via USPS or FedEx. Books must be ....</notes>
        <prices>
            <price condition="new">13.55</price>
            <price condition="used">13.55</price>
            </prices>
        <link> http://partners.campusbooks.com/link.php?params=b3...</link>
    </merchant>

To this:

    example: "<pre><code>" +
      "&lt;merchant&gt;" +
      "    &lt;merchant_id&gt;108&lt;/merchant_id&gt;" +          "    &lt;merchant_image&gt;http://www.campusbooks.com/images/markets/firstclassbooks.gif&lt;/merchant_iamge&gt;" +          "    &lt;name&gt;First Class Books&lt;/name&gt;" +
      "    &lt;notes&gt;Free shipping via USPS or FedEx. Books must be ....&lt;/notes&gt;" +
      "    &lt;prices&gt;" +
      "        &lt;price condition=\"new\"&gt;13.55&lt;/price&gt;" +
      "        &lt;price condition=\"used\"&gt;13.55&lt;/price&gt;" +
      "        &lt;/prices&gt;" +
      "    &lt;link&gt; http://partners.campusbooks.com/link.php?params=b3...&lt;/link&gt;" +
      "&lt;/merchant&gt;" +
    "</code></pre>"

