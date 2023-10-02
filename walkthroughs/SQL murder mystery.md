tables:

|crime_scene_report|
|---|
|drivers_license|
|facebook_event_checkin|
|interview|
|get_fit_now_member|
|get_fit_now_check_in|
|solution|
|income|
|person|

[#] commad - select * from sqlite_master where type='table';

-------------------------------------------------------------------------------------------------------

second step says: to filter like this

SQL City  
20180115	  
murder

[#]command: select * from crime_scene_report where city='SQL City' and date='20180115';

-------------------------------------------------------------------------------------------------------------------------------------------------------

result:

Security footage shows that there were 2 witnesses. The first witness lives at the last house on "Northwestern Dr". The second witness, named Annabel, lives somewhere on "Franklin Ave".

so let's fiirst check on annabel:

[#] command: select * from person where name LIKE '%Annabel%' and address_street_name='Franklin Ave';

results:

|id|name|license_id|address_number|address_street_name|ssn|
|---|---|---|---|---|---|
|16371|Annabel Miller|490173|103|Franklin Ave|318771143|

then for the last house we need to find the max address_number: ie. = 4919

[#]command: select MAX(address_number) from person where address_street_name='Northwestern Dr';

then we find that person:

[#] command: select * from person where address_street_name='Northwestern Dr' and address_number='4919';

result:

|id|name|license_id|address_number|address_street_name|ssn|
|---|---|---|---|---|---|
|14887|Morty Schapiro|118009|4919|Northwestern Dr|111564949|

--------------------------------------------------------------------------------------------------------------------------------

then we check at the interview with the person ID we got:

14887 and 16371

via:

[#] select * from interview where person_id='14887' UNION select * from interview where person_id='16371';

result:

14887: I heard a gunshot and then saw a man run out. He had a "Get Fit Now Gym" bag. The membership number on the bag started with "48Z". Only gold members have those bags. The man got into a car with a plate that included "H42W".

16371: I saw the murder happen, and I recognized the killer from my gym when I was working out last week on January the 9th.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------

now we go to the gym check in table with the above info:

[#] select * from get_fit_now_check_in where membership_id LIKE '%48Z%' and check_in_date='20180109';

result:

|membership_id|check_in_date|check_in_time|check_out_time|
|---|---|---|---|
|48Z7A|20180109|1600|1730|
|48Z55|20180109|1530|1700|

[#] select * from drivers_license where plate_number LIKE '%H42W%';

|id|age|height|eye_color|hair_color|gender|plate_number|car_make|car_model|
|---|---|---|---|---|---|---|---|---|
|183779|21|65|blue|blonde|female|H42W0X|Toyota|Prius|
|423327|30|70|brown|brown|male|0H42W2|Chevrolet|Spark LS|
|664760|21|71|black|black|male|4H42WR|Nissan|Altima|

now we found some id's in the above check table so we can check the gym member table:

select * from get_fit_now_member where id='48Z7A' UNION select * from get_fit_now_member where id='48Z55';

|id|person_id|name|membership_start_date|membership_status|
|---|---|---|---|---|
|48Z55|67318|Jeremy Bowers|20160101|gold|
|48Z7A|28819|Joe Germuska|20160305|gold|

look at their interview:

|67318|I was hired by a woman with a lot of money. I don't know her name but I know she's around 5'5" (65") or 5'7" (67").<br><br>She has red hair and she drives a Tesla Model S. I know that she attended the SQL Symphony Concert 3 times<br><br>in December 2017.|
|---|---|

so he is the hired mercenery ... we are getting close....

------------------------------------------------------------------------------------------------------------------------------------------------------------------

looking at the details we got:

[#] select * from drivers_license where height='65' and hair_color='red' and car_make LIKE '%Tesla%';

|id|age|height|eye_color|hair_color|gender|plate_number|car_make|car_model|
|---|---|---|---|---|---|---|---|---|
|918773|48|65|black|red|female|917UU3|Tesla|Model S|

now we need to check the facebook_event_checkin for this concert and dig persons who attended this event with given details:

[#] select * from facebook_event_checkin where date LIKE '%201712%' and event_name LIKE '%SQL Symphony Concert%';

|person_id|event_id|event_name|date|
|---|---|---|---|
|62596|1143|SQL Symphony Concert|20171225|
|19260|1143|SQL Symphony Concert|20171214|
|58898|1143|SQL Symphony Concert|20171220|
|69699|1143|SQL Symphony Concert|20171214|
|19292|1143|SQL Symphony Concert|20171213|
|43366|1143|SQL Symphony Concert|20171207|
|92343|1143|SQL Symphony Concert|20171212|
|28582|1143|SQL Symphony Concert|20171220|
|28582|1143|SQL Symphony Concert|20171215|
|81526|1143|SQL Symphony Concert|20171202|
|24397|1143|SQL Symphony Concert|20171208|
|11173|1143|SQL Symphony Concert|20171223|
|79312|1143|SQL Symphony Concert|20171203|
|69325|1143|SQL Symphony Concert|20171206|
|24556|1143|SQL Symphony Concert|20171207|
|24556|1143|SQL Symphony Concert|20171221|
|24556|1143|SQL Symphony Concert|20171224|
|99716|1143|SQL Symphony Concert|20171206|
|99716|1143|SQL Symphony Concert|20171212|
|99716|1143|SQL Symphony Concert|20171229|
|67318|1143|SQL Symphony Concert||

we need to look among these who has mass amount of wealth and the person's a “she”

look for 3 times repeating id in above table:

SELECT person_id, COUNT(person_id) AS value1_count

FROM facebook_event_checkin

GROUP BY person_id

HAVING COUNT(*) = 3 and event_name='SQL Symphony Concert' and date LIKE '%201712%'

person_Id value

|19292|3|
|---|---|
|24556|3|
|58898|3|
|99716|3|

|person_id|transcript|
|---|---|
|19292|‘Their heads are gone, if it please your Majesty!’ the soldiers shouted|

|person_id|transcript|
|---|---|
|58898|question, you know.’|