# PMGSY Data <> OSM comparison app

Live website: https://projects.datameet.org/pmgsy_osm_comparison/
OpenAPI doc: https://server.nikhilvj.co.in/pmgsy/docs  

This app (a backend + a webpage frontend) aims to bridge gaps between PMGSY data release and OpenStreetMap data by displaying both in one place and showing differences between them.


## Phase 1
- Starting with Habitations data
- A read-only interface for now where user can select the block and data within that block is fetched
- Data is displayed in both map and table form, with interaction between the two
- Doing distance comparison and highlighting the OSM rural places that are far away from PMGSY Habitations
- Choose a block either by using dropdowns, or right-click / press-hold on the map and load the block at that lat-long.


## Phase 2
- Feedback functionality added for collecting feedback on PMGSY Habitations and OSM rural places
- IP logging and rate limiting introduced to prevent jamming of backend due to bots / scraping but keeping access open for public
- Improvements in permalink action for sharing
- OSM comparison action made optionally automatic via a checkbox and permalink param
- Feedback currently collected in backend DB
- SOI map background layer introduced, credits: tile layer created by https://github.com/ramSeraph/opendata/ , orig source: https://onlinemaps.surveyofindia.gov.in/FreeMapSpecification.aspx 


## Notes / Caveats:
- A "far away" place at the border might be near to a habitation on the other side of the border in an adjoining block. So, judge at your own discretion
- Thick black border on the map is India int'l boundary as per shapefile shared on https://surveyofindia.gov.in/pages/outline-maps-of-india . Precision of the same is lesser at higher zooms, but it's official so going with it. If you want it fixed, pls go get it fixed at the mentioned official govt website first.
- There are some cases of adjoining blocks having swapped either their block boundaries or block ids with each other. This is as-is from the blocks shapefile shared in PMGSY data release and would be a fluke on their side.


## Tech stack
- Python FastApi framework used to make the backend and serve the static frontend
- Vanilla HTML/JS for frontend
- Database on PostGreSQL, deployed via docker
- Geopandas / shapely python libs used for various geospatial functions
- Hosted on a SSDNodes server


## Credits
Started in April 2022 by Nikhil VJ (answerquest), Kaisyn Consultancy and Training Pvt Ltd.  

Using data from [PMGSY Open Data Release](https://geosadak-pmgsy.nic.in/opendata),  
Collected at https://github.com/datameet/pmgsy-geosadak,  
And imported into a PostGreSQL DB as per this script https://github.com/answerquest/import_geosadak made by me earlier.  

Tip jar : Like this work? You can pay a tip to the project's maintainers at their ids below:  
Nikhil : **UPI: 9766692835@okbizaxis**


## Stats file

There are 2 python scripts here that aren't part of the fastapi server program:
- habitation_stats.py
- habitation_stats_embellish.ipynb

These were used to loop over every habitations block, compare PMGSY and OSM data, and output a stats dump:  
**stats_habitations_vs_OSM_v2.csv**

One can use this CSV directly for getting a bird's eye view of the state of PMGSY vs OSM rural habitations mapping in all of India.

Columns:

- hab_num: number of PMGSY mapped habitations
- hab_outside: number of habitations outside of block boundary
- osm_num: number of OSM rural places
- greater: were there more habitations in this block or more OSM places
- osm_near: number of OSM places that are within 1km of nearest habitation
- osm_far: number of OSM places that are more than 1km from habitation
- hab_near: number of PMGSY habitations that have an OSM place within 1km distance
- hab_far: number of PMGSY habitations that don’t have any OSM place within 1km distance
- hab_osm_diff: Habitations - OSM places (negative if OSM places greater in the block)
- lat, lon: Centroid of the block
- no_shape: 1 - Boundary for this block wasn't present in the PMGSY dataset, so was assumed by taking convex hull of the habitations and applying 1km buffer. 0 - nothing
