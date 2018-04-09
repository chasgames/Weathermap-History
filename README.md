# What is this?

This repo is just the web html to display the animated weathermap GIFs based on chosen date.

The guide below will walk you through the shell scripts to creates animations of the weathermap daily  (GIF Format)

---

## Prerequisites
- Access to the "convert" command (yum install ImageMagick , and on ubuntu apt install imagemagick ) - used to convert pngs to gif
- (Optional) pngquant (yum install pngquant , apt install pngquant) - PNG Optimization
- (Optional) gifsicle (yum install gifsicle, apt install gifsicle) - GIF Optimization (shows a GIF filesize reduction of 87%)
- Assumes your librenms path is /opt/librenms (otherwise you will need to edit the scripts)

---

##  How do I use this?

mkdir -p /opt/weathermap-history/history

cd /opt/weathermap-history

vim getweather.sh (edit MAPNAME.png and file path if wrong)
```
#!/bin/bash
DATE=`date '+%y%m%d%H%M'`
cp /opt/librenms/html/plugins/Weathermap/output/MAPNAME.png /opt/weathermap-history/history/MAPNAME_$DATE.png
```
chmod +x getweather.sh

vim makeGIF.sh
```
#!/bin/bash

DATE=$(date "+%Y-%m-%d" -d "yesterday")

cd /opt/weathermap-history/history/
mkdir -p /opt/librenms/html/plugins/Weathermap/output/history/$DATE
pngquant --speed 9 *.png -f --ext .png
convert  -delay 30 -loop 0 *.png /opt/librenms/html/plugins/Weathermap/output/history/$DATE/$DATE.gif
gifsicle -O3 /opt/librenms/html/plugins/Weathermap/output/history/$DATE/$DATE.gif -o /opt/librenms/html/plugins/Weathermap/output/history/$DATE/$DATE.gif
rm /opt/weathermap-history/history/*
```
chmod +x makeGIF.sh

Open Roots cronjob

crontab -e 

(First line will create PNGs at 5 minute intervals between 18:00 up to 23:55, change this to your busiest periods)
(Second line creates the GIF at 3am)

```
*/5 18-23 * * * /opt/weathermap-history/getweather.sh >> /dev/null 2>&1
0 3 * * * /opt/weathermap-history/makeGIF.sh >> /dev/null 2>&1
```

cd /opt/librenms/html/plugins/Weathermap/output


Now clone the repo
```
git clone https://github.com/chasgames/Weathermap-History history
```

In 24 hours you should have the date folder and GIF created in this folder opt/librenms/html/plugins/Weathermap/output/history/

You can go to the web front end here:
http://1.1.1.1/plugins/Weathermap/output/history/index.html


---

##  Acknowledgments
- https://github.com/CaptainCodeman/gif-player (MIT)
