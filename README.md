(Optional) pngquant (e.g yum install pngquant) - PNG Optimization

mkdir -p /opt/weathermap-history/history

cd /opt/weathermap-history

vim getweather.sh (edit MAPNAME.png and file path if wrong)
```
#!/bin/bash
DATE=`date '+%y%m%d%H%M'`
cp /opt/librenms/html/plugins/Weathermap/output/MAPNAME.png /opt/weathermap-history/history/MAPNAME_$DATE.png
```

vim makeGIF.sh
```
#!/bin/bash

DATE=$(date "+%Y-%m-%d")

cd /opt/weathermap-history/history/
mkdir -p /opt/librenms/html/plugins/Weathermap/output/history/$DATE
pngquant --speed 9 *.png -f --ext .png
convert  -delay 30 -loop 0 *.png /opt/librenms/html/plugins/Weathermap/output/history/$DATE/$DATE.gif
rm /opt/weathermap-history/history/*
```

mkdir -p /opt/librenms/html/plugins/Weathermap/output/history
cd /opt/librenms/html/plugins/Weathermap/output/history

git clone https://github.com/chasgames/Weathermap-History
