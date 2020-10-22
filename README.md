# wf-relics
 
You can see a working version here: https://wf-relics.vercel.app/

The biggest problem is that you have to deactivate cors in your browser to use the live version because you have to make requests to the warframe market api. I could do them from the server side but then the IP will get rate limited at some point (if multiple users would use the page at the same time).
A fix for that would be a caching solution which could sadly lead to invalid/old prices.
Or maybe another endpoint with all prices? Not quite sure yet.
