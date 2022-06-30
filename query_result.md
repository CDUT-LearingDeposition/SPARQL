1.

query1:

```
prefix dc: <http://purl.org/dc/elements/1.1/>
prefix gts: <http://resource.geosciml.org/ontology/timescale/gts#>
prefix skos: <http://www.w3.org/2004/02/skos/core#>
prefix time: <http://www.w3.org/2006/time#>
prefix ts: <http://resource.geosciml.org/vocabulary/timescale/> 
prefix isc: <http://resource.geosciml.org/classifier/ics/ischart/> 

SELECT   DISTINCT ?schemeID str(?label) AS ?geoConcpet ?begTimeValue ?endTimeValue 
WHERE
{
 GRAPH <http://deeptimekb.org/iscallnew> 
 {
        {       
         ?tconcept  a gts:GeochronologicEra ;  
                         rdfs:label ?label ; 
                         skos:inScheme ?schemeID.
         FILTER (lang(?label) = "en")
         FILTER strstarts(?label, "Wordian").
         ?tconcept time:hasBeginning ?beg ;
         time:hasEnd ?end .
         ?beg time:inTemporalPosition ?begTime .
         ?end time:inTemporalPosition ?endTime .
         ?begTime time:numericPosition ?begTimeValue .
         ?endTime time:numericPosition ?endTimeValue .
         }
   UNION
         {
         FILTER (lang(?label) = "en")
         FILTER strstarts(?label, "Wordian").    
         
         ?tconcept a gts:GeochronologicEra ; 
                   rdfs:label ?label ;
          dc:description
         [time:hasBeginning ?beg ;
          skos:inScheme  ?schemeID].

          ?beg time:inTemporalPosition ?begTime .
 
          ?begTime dc:description
          [time:numericPosition ?begTimeValue ;
          skos:inScheme  ?schemeID].


         ?tconcept a gts:GeochronologicEra ; 
                   rdfs:label ?label ;
          dc:description
         [time:hasEnd ?end ;
          skos:inScheme  ?schemeID].

          ?end time:inTemporalPosition ?endTime .

          ?endTime dc:description
          [time:numericPosition ?endTimeValue ;
          skos:inScheme ?schemeID].
         }
 }
}
ORDER BY DESC (?schemeID)
```

result：

![image-20220630160021016](..\query_result.assets\image-20220630160021016.png)

query2：

```
prefix dc:  <http://purl.org/dc/elements/1.1/>
prefix gts: <http://resource.geosciml.org/ontology/timescale/gts#>
prefix skos: <http://www.w3.org/2004/02/skos/core#>
prefix time: <http://www.w3.org/2006/time#>
prefix ts: <http://resource.geosciml.org/vocabulary/timescale/>
prefix isc: <http://resource.geosciml.org/classifier/ics/ischart/>

SELECT  ?schemeID str (?label)  AS ?geoConcept ?begTimeNum ?endTimeNum (?begTimeNum - ?endTimeNum) AS ?duration
WHERE
{
        GRAPH <http://deeptimekb.org/iscallnew>
        {
        {
        ?geoconcept rdfs:label ?label ;
              dc:description
              [
              time:hasBeginning ?beg ;
              time:hasEnd  ?end
              ].
         ?beg time:inTemporalPosition ?begTime .
         ?end time:inTemporalPosition ?endTime .

         ?begTime dc:description
               [
               time:numericPosition ?begTimeNum ;
               skos:inScheme ?schemeID
               ].
         ?endTime dc:description
               [
               time:numericPosition ?begTimeNum ;
               skos:inScheme ?schemeID
               ].
         FILTER(?begTimeNum <=10)
         FILTER(?begTimeNum >= 10) 
         }
        UNION
        {
           ?geoConcept  rdfs:label ?label;
                                 time:hasBeginning ?beg ;
                                 time:hasEnd ?end ;
                                 skos:inScheme  ?schemeID .
           ?beg time:inTemporalPosition ?begTime .
           ?end time:inTemporalPosition ?endTime .
           ?begTime time:numericPosition ?begTimeNum .
           ?endTime time:numericPosition ?endTimeNum .
           FILTER(?begTimeNum <= 10)
           FILTER(?endTimeNum >= 10) 
        }
        }
}
ORDER BY ?schemeID

```

result：

![image-20220630160421262](.\query_result.assets\image-20220630160421262.png)



query3：

```
prefix dc: <http://purl.org/dc/elements/1.1/>
prefix gts: <http://resource.geosciml.org/ontology/timescale/gts#>
prefix skos: <http://www.w3.org/2004/02/skos/core#>
prefix time: <http://www.w3.org/2006/time#>
prefix ts: <http://resource.geosciml.org/vocabulary/timescale/>
prefix isc: <http://resource.geosciml.org/classifier/ics/ischart/>

SELECT DISTINCT ?schemeID str (?label) AS ?geoConcept
WHERE
{
GRAPH <http://deeptimekb.org/iscallnew>
{
                 {
                 ?tconcept a gts:GeochronologicEra ;
                                        rdfs:label ?label ;
                 time:hasBeginning ?beg ;
                 time:hasEnd ?end ;
                 skos:inScheme ?schemeID.
                 ?beg time:inTemporalPosition ?begTime.
                 ?end time:inTemporalPosition ?endTime.
                 ?begTime time:numericPosition ?begTimeValue.
                 FILTER(?begTimeValue <= 20 )
                 ?endTime time:numericPosition ?endTimeValue.
                 FILTER(?endTimeValue >= 13) .
                 }
UNION
                 {
                 ?tconcept a gts:GeochronologicEra ;
                             rdfs:label ?label.
                 ?tconcept dc:description
                 [time:hasBeginning ?beg;
                  time:hasEnd ?end;
                 skos:inScheme ?schemeID].
                 ?beg time:inTemporalPosition ?begTime.
                 ?end time:inTemporalPosition ?endTime.
                 ?begTime dc:description
                 [time:numericPosition ?begTimeValue;
                 skos:inScheme ?schemeID].
                 ?endTime dc:description
                 [time:numericPosition ?endTimeValue;
                 skos:inScheme ?schemeID].
                 FILTER(?begTimeValue <= 20 ).
                 FILTER(?endTimeValue >= 13 ) .
                 }
 }
 }

```

result:

![image-20220630160330153](query_result.assets\image-20220630160330153.png)

