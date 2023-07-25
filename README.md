# SIBO-Sat_Sync
Setting the system clock in Psion SIBO computers via an attached GPS

---
![Series 3a Sync'd via GPS](https://zedstarr.files.wordpress.com/2023/07/sat_sync.png)

Psion MC/Series 3/3a time setting via serially attached GPS receiver that outputs NMEA

Uses the CALL $058B to retrieve the "country data" structure - this includes UTC offset (in minutes) and "Summer Time" flag

NOTE: **DATE IS NOT SET - ONLY TIME!**

After opening the serial port we 
- get 255 bytes of NMEA 
- LOCate GPRMC, get GPS traceable UTC time from next 7-12 bytes
- calc time (in seconds from 1/1/1970) from that and UTC offset and "Summer Time"
- Add a "delay processing time" fudge factor of ... 1s?
- call OS function $038B to set the time

Time is expressed as:

1690166270 or $64BDE3F3 (32-bit number) - i.e. can be split into $64BD & $E3F3 as below

(as Hex $nnnnNNNN - split into hi & low part $nnnn & $NNNN or just by simple maths ;-) )

 
Tested working on the MC/Series 3/Series 3a
 
Assumes a GPS receiver attached to the serial port, running at 9600 8N1
Assumes a GPS fix
 
Assumes GPRMC or GPGGA sentences which contain UTC time after "GPRMC," or "GPGGA,"
 
> $GPRMC,170805.000,A,5337.5945,N,00300.7381,W,0.00,281.17,250523,,,D*7D
> $GPVTG,281.17,T,,M,0.00,N,0.01,K,D*34
> $PGTOP,11,3*6F
> $GPGGA,170806.000,5337.5945,N,00300.7381,W,2,9,1.13,4.2,M,49.5,M,0000,0000*40
 
 
The "GenGetCountryData" OS call may or may not be meaningless on the MC - certainly its clock has no concept of "Summer Time"
like the 3/3a does. So `stime%` is set to 1 in the MC's OPL to get UTC to BST working (in summer!). Also (obviously!) all the 
graphics commands such as `gAT`, `gPRINT`, etc. are meaningless on the MC (I also removed them from the Series 3 code too). 

I also had to change the sentence to search for to GPGGA rather than GPRMC on the S3 & MC, possibly something to do with 
serial overruns or maybe something else entirely...?

### **DISCLAIMER**: THIS IS BARELY WORKING CODE! There is minimal error checking, lot's of superflous variables & commented-out test code

### **USE AT YOUR OWN RISK!**   
Shared in the hope it might prove useful to someone. somewhere ;-)


 :: Chris Farrow @zedstarr July 2023 ::

![Series 3 sync'd via GPS](https://zedstarr.files.wordpress.com/2023/07/20230724_161316.jpg?w=1024)
---
![Even the old MC400 gets synchronised via GPS](https://zedstarr.files.wordpress.com/2023/07/screen2023-07-24-165052.png)

