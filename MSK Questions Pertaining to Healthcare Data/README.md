## The following queries and questions related to musculoskeletal (MSK) healthcare data were executed for additional practice and a step to a larger personalized project. 

###  What are the muscle -related diagnosis descriptions? Who were the patients that presented with muscle related issues? How many visits occurred related to this issue? 
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
#### Output
<img src="MSKpatients.png" alt="MSKpatients" style="width:600px;height:300px;">

