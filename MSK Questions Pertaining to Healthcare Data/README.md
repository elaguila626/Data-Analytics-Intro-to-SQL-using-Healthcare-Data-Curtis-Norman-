## The following queries and questions related to musculoskeletal (MSK) healthcare data were executed for additional practice and are a step toward a larger personalized project. Each query has a sample SQL output and tableau dashboard presenting the data. 

###  How many patients are there per provider specialty within each hospital between 2019-2020? 
#### Query

SELECT locationname, providerspecialty, COUNT(DISTINCT(dimpatient)) AS numofpatients 
<br> FROM facttable
<br> INNER JOIN dimpatient
<br> ON facttable.dimpatientpk = dimpatient.dimpatientpk
<br> INNER JOIN dimphysician
<br> ON facttable.dimphysicianpk = dimphysician.dimphysicianpk
<br> INNER JOIN dimlocation
<br> ON dimlocation.dimlocationpk = facttable.dimlocationpk
<br> GROUP BY locationname, providerspecialty
<br> ORDER BY locationname;

#### SQL Output

![Numofptsperhospital specialty](https://github.com/elaguila626/Data-Analytics-Intro-to-SQL-using-Healthcare-Data-Curtis-Norman-/assets/100698925/f83b2c8c-1ea6-4c59-84c7-e859fb79351f)


#### Tableau Demonstration

https://github.com/elaguila626/Data-Analytics-Intro-to-SQL-using-Healthcare-Data-Curtis-Norman-/assets/100698925/5a260fc8-1a96-4fb5-af04-65407dc2a450

###  What are the MSK-related diagnosis descriptions? Who were the patients that presented with MSK-related issues? How many visits occurred related to this issue? 

#### Query
SELECT firstname,lastname,diagnosiscodedescription, 
<br>COUNT(facttable.dimdateservicepk) AS numberofvisits
<br>FROM facttable
<br>INNER JOIN dimdiagnosiscode
<br>ON dimdiagnosiscode.dimdiagnosiscodepk = facttable.dimdiagnosiscodepk
<br>INNER JOIN dimpatient
<br>ON dimpatient.dimpatientpk = facttable.dimpatientpk
<br>WHERE diagnosiscodedescription ILIKE '%muscle%'
<br>OR diagnosiscodedescription ILIKE '%fracture%'
<br>OR diagnosiscodedescription ILIKE '%strain%'
<br>OR diagnosiscodedescription ILIKE '%sprain%'
<br>GROUP BY firstname,lastname,diagnosiscodedescription
<br>ORDER BY numberofvisits DESC;

#### SQL Output

#### Tableau Demonstration

