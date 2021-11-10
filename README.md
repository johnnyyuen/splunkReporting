# splunkReporting
Use Splunk to generate nice report


---
Use map with Country Code

    (index=<**Your Index 1**> OR index=<**Your Index 2**>) source=<**Your Source**>
     | table  DISPLAYNAME, CC, LASTUPDINVSVR, OSTYPE, Model, PosType, SUITENAME, VERSION, INSTALLDATE, ADDRESS
     | search $fieldCC$ ($fieldModel$ OR NOT MODEL=*) $fieldPosType$  SUITENAME ="ESET Endpoint Security"
     | dedup  DISPLAYNAME
     | stats count(eval(match(OSTYPE,".*8.*"))) as Win8, count(eval(match(OSTYPE,".*10.*"))) as Win10 BY CC
     | rename CC AS iso2 
     | lookup geo_attr_countries iso2 OUTPUT country
     | eval percentage=round((Win10/(Win8 + Win10))*100,1)
     | fields percentage, country
     | geom geo_countries featureIdField="country"


![An screen cap for map](/worldmap_status.png)


Use mutliple bar chart


    (index=<**Your Index 1**> OR index=<**Your Index 2**>) source=<**Your Source**>
    | table  DISPLAYNAME, CC, LASTUPDINVSVR, OSTYPE, Model, PosType, SUITENAME, VERSION, INSTALLDATE, ADDRESS
    | search $fieldHost$ $fieldCC$ ($fieldModel$ OR NOT Model=*) ($fieldPosType$ OR NOT PosType=*)  SUITENAME ="ESET Endpoint Security"
    | dedup  DISPLAYNAME
    | stats count(eval(NOT match(Model, ".*I1.*"))) AS "Non Beetle I1", count(eval(match(Model, ".*I1.*"))) AS "Beetle I1" by CC
    | sort - count "Beetle I1"

![An screen cap for bar chart](/status_dualBar.png)
