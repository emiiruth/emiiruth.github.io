1. Developer runs:

npm install lodash

2. Request goes through ZCC -> ZIA (as all requests do xx)

3. URL Category matches on DESTINATION REGISTRY (e.g. node, pypi, etc) - > Forwarding Control rule matches + URL Control rule matches 
    a. Forwarding Control -> Traffic Forwarded to Proxy Gateway
        Triggers SSL Inspection (allows glow to read unencrypted traffic)
        Forwards to Glow URL (after URL control modifies header)  
    b. URL Control
        HTTP Header Profile is applied (labels traffic as from PTC)
This is the networking exchange between PTC Zscaler and Glow where mutual certificate trust is necessary

4. Glow forwards request to npm

5. npm returns metadata

6. Glow evaluates metadata against its own intelligence
    - Package reputation
    - Package age
    - Known malware
    - Typosquatting
    - Organizational policy

7. Glow rewrites response if needed - modifies npm metadata to exclude option to install bad stuff

8. Modified response metadata returned to user device via zscaler 

9. local npm reads response metadata and downloads accordingly
    if glow has blocked the package, npm response will indicate it doesnt exist
    if we're rolling a version back it will see the approved version as latest to download


We're good on the url cascading issue (don't need it on) because we're not explicitly allowing any cloud apps that would get in the way of this (unless edge add ons are getting caught in the microsoft one click rules)