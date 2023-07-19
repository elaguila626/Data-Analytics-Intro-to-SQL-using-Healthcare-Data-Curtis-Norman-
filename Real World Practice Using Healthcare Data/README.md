# Healthcare Data SQL Practice Questions 

### 1. How many rows of data are in the FactTable that include a Gross Charge greater than $100? 
#### Query
SELECT count(grosscharge) AS CountOfRows 
<br>FROM facttable
<br>WHERE grosscharge > 100.00;
#### Output
-- 5513

### 2. How many unique patients exist in the Healthcare_DB?
#### Query
SELECT COUNT(DISTINCT(patientnumber)) AS NumberOfUniquePatients 
<br>FROM dimpatient;
#### Output
-- 4962

### 3. How many CptCodes are in each CptGrouping?
#### Query
SELECT cptgrouping, COUNT(DISTINCT(cptcode)) AS CountOfCptCodes
<br> FROM dimcptcode
<br> GROUP BY cptgrouping
<br> ORDER BY 2 DESC;

#### Output
<img src="countofcptcodes.png" alt="countcpt" style="width:300px;height:228px;">

### 4. How many providers have submitted a Medicare insurance claims?
#### Query
SELECT COUNT(DISTINCT(providernpi)) AS countofproviders
<br>FROM facttable 
<br>INNER JOIN dimphysician
<br>ON dimphysician.dimphysicianpk = facttable.dimphysicianpk
<br>INNER JOIN dimpayer
<br>ON dimpayer.dimpayerpk = facttable.dimpayerpk
<br>WHERE payername = 'Medicare';

#### Output
-- 682

### 5. Calculate the gross collection rate for each location name - see below; GCR = payments divided gross charge. Which locationname has the highest GCR? 
#### Query
SELECT locationname, 100 *(-SUM(payment)/ sum(grosscharge)) AS GCR_Percent
<br> FROM facttable
<br> INNER JOIN dimlocation
<br> ON dimlocation.dimlocationpk = facttable.dimlocationpk
<br> GROUP BY  locationname
<br> ORDER BY 2 DESC;
#### Output
<img src="gcrpercent.png" alt="gcrperc" style="width:300px;height:228px;">

### 6. How many CptCodes have more than 100 units?
#### Query
SELECT COUNT(*) AS cptwithgreaterthanhundredunits
<br> FROM(
<br>	SELECT cptcode, SUM(cptunits)
<br>	FROM facttable 
<br>	INNER JOIN dimcptcode
<br>	ON dimcptcode.dimcptcodepk = facttable.dimcptcodepk
<br>	GROUP BY cptcode
<br>	HAVING SUM(cptunits) > 100) AS a; 
#### Output
-- 29

### 7. Find the physician specialty that has received the highest amount of payments. Then show the payments by month for this group of physicians. 
#### Query
SELECT providerspecialty, monthperiod, monthyear,  -SUM(payment) AS payments
<br>FROM facttable
<br>INNER JOIN dimdate
<br>ON dimdate.dimdatepostpk = facttable.dimdatepostpk
<br>INNER JOIN dimphysician
<br>ON dimphysician.dimphysicianpk = facttable.dimphysicianpk
<br>WHERE providerspecialty = 'Internal Medicine'
<br>GROUP BY providerspecialty, monthperiod, monthyear
<br>ORDER BY monthperiod;
#### Output
<img src="physspec.png" alt="physcianspecialty" style="width:350px;height:228px;">

### 8. How many Cpt Units by DiagnosisCodeGroup are assigned to a "J code" diagnosis? 
#### Query
SELECT diagnosiscodegroup, SUM(cptunits) as totalunits
<br>FROM facttable
<br>INNER JOIN dimdiagnosiscode
<br>ON dimdiagnosiscode.dimdiagnosiscodepk = facttable.dimdiagnosiscodepk
<br>WHERE diagnosiscode ILIKE '%j%'
<br>GROUP BY diagnosiscodegroup
<br>ORDER BY totalunits;
#### Output
<img src="jdiagnosis.png" alt="jdiagnosis" style="width:350px;height:228px;">

### 9. You've been asked to put together a report that details patient demographics. The report should group patients into three buckets - under 18, between 18-65, and over 65. 
#### Query
SELECT firstname || ' ' || lastname AS Name, email, patientage,
<br> CASE 
<br> WHEN (patientage < 18) THEN '< 18'
<br> WHEN (patientage BETWEEN 18 AND 65) THEN '18-65'
<br> WHEN (patientage > 65) THEN '> 65'
<br> ELSE null 
<br> END AS Patientagebucket,
<br> city || ' ' || state AS Cityandstate
<br> FROM dimpatient;
#### Output
<img src="samplepatientdemographic.png" alt="ptdemo" style="width:350px;height:350px;">

### 10. How many dollars have been written off (adjustments) due to credentialing (adjustment reason)? Which location has the highest number of credentialing adjustments? How many physicians at this location have been impacted by credentialing adjustments? What does this mean?
#### Query
SELECT locationname, COUNT(DISTINCT(providernpi)) AS physiciansimpacted, - SUM(adjustment) AS totaladjustment 
<br> FROM facttable
<br> INNER JOIN dimtransaction
<br>ON dimtransaction.dimtransactionpk = facttable.dimtransactionpk
<br> INNER JOIN dimlocation
<br> ON dimlocation.dimlocationpk = facttable.dimlocationpk
<br> INNER JOIN dimphysician
<br>ON dimphysician.dimphysicianpk = facttable.dimphysicianpk
<br>WHERE transactiontype = 'Adjustment' AND  adjustmentreason = 'Credentialing'
<br>GROUP BY locationname;
#### Output
<img src="adjustments.png" alt="adjustments" style="width:350px;height:300px;">
-- What does this mean? Based on the data there are a total of 30 physicians at 'Anglestone Community Hospital' who are not credentialed and allowing a total of $2106.79 to be written off. 

### 11. What is the average patientage by gender for patients seen at Big Heart Community Hospital with a Diagnosis that included Type 2 Diabetes? And how many patients are included in that average? 

#### Query
SELECT patientgender, COUNT(DISTINCT(dimpatient.patientnumber)) AS totalpatients, 
<br> ROUND(AVG(patientage),2) AS avgpatientage
<br> FROM facttable
<br>INNER JOIN dimlocation
<br>ON dimlocation.dimlocationpk = facttable.dimlocationpk
<br>INNER JOIN dimpatient
<br>ON dimpatient.dimpatientpk = facttable.dimpatientpk
<br>INNER JOIN dimdiagnosiscode
<br>ON dimdiagnosiscode.dimdiagnosiscodepk = facttable.dimdiagnosiscodepk
<br>WHERE diagnosiscodedescription ILIKE '%type 2%' AND
<br>locationname ILIKE '%Big Heart Community Hospital%'
<br>GROUP BY patientgender;
#### Output
<img src="type2patients.png" alt="type2patients" style="width:300px;height:75px;">

### 12. There are a two visit types that you have been asked to compare (use CptDesc).  Office outpateint visit  est vs new. Show each Cpt Code, Cpt desc, and the associated Cpt units. What is the charge per Cpt Units? What does this mean?
#### Query
SELECT cptcode, cptdesc AS cptoutpatient, 
<br>SUM(cptunits) AS cptunits, ROUND(SUM(grosscharge)/SUM(cptunits),2) AS chargeperunit
<br>FROM facttable
<br>INNER JOIN dimcptcode
<br>ON dimcptcode.dimcptcodepk = facttable.dimcptcodepk
<br>WHERE cptdesc ILIKE '%outpatient%est%' OR cptdesc ILIKE '%outpatient%new%'
<br>GROUP BY cptcode, cptdesc
<br>ORDER BY cptunits;
#### Output
<img src="chargepercptunit.png" alt="chargepercptunit" style="width:400px;height:300px;">


### 13.Analyze paymentperunit by payername. Complete this analysis by the following visit type (CptDesc) Initial Hospital Care. 

#### Query
SELECT payername, cptcode, cptdesc AS cptoutpatient, 
<br>SUM(cptunits) AS cptunits, ROUND(-SUM(payment)/NULLIF(SUM(cptunits),0),2) AS paymentperunit
<br>FROM facttable
<br>INNER JOIN dimcptcode
<br>ON dimcptcode.dimcptcodepk = facttable.dimcptcodepk
<br>INNER JOIN dimpayer
<br>ON dimpayer.dimpayerpk = facttable.dimpayerpk
<br>WHERE cptdesc ILIKE '%initial%hospital%care%'
<br>GROUP BY payername, cptcode, cptdesc
<br>ORDER BY cptunits;
#### Output
<img src="paymentpername.png" alt="paymentpername" style="width:400px;height:300px;">


### Find the NetCharge (Gross Charges - Contractual Adjustments). Calculate the Net Collection Rate (Payments/Netcharge) for each speciality. Which a has the worst NCR w/ a NC greater than $25,000?

#### Query
SELECT providerspecialty, 
<br>grosscharges, 
<br>ontractualadjustment, 
<br>	netcharge, 
<br>	payments, 
<br>	adjustments - contractualadjustment AS adjustments,
<br>	ROUND(100*(-payments/netcharge)) AS netcollectionrate, 
<br>	AR,
<br>	ROUND(100*(AR/netcharge)) AS percentinAR,
<br>	ROUND(-100*(adjustments - contractualadjustment)/netcharge) AS writeoffpercent
<br>FROM
<br>	(SELECT providerspecialty,
<br>	SUM (grosscharge) AS grosscharges, 
<br>	SUM(CASE 
<br>	WHEN adjustmentreason = 'Contractual' THEN adjustment
<br>	ELSE Null
<br>	END) AS contractualadjustment,
<br>	(SUM(grosscharge) + SUM(CASE 
<br>	WHEN adjustmentreason = 'Contractual' THEN adjustment
<br>	ELSE Null
<br>	END)) AS netcharge,
<br>	SUM (payment) AS payments,
<br>	SUM (adjustment) AS adjustments, 
<br>	SUM (ar) AS AR
<br>	FROM facttable
<br>	INNER JOIN dimphysician
<br>	ON dimphysician.dimphysicianpk = facttable.dimphysicianpk
<br>	INNER JOIN dimtransaction
<br>	ON dimtransaction.dimtransactionpk = facttable.dimtransactionpk
<br>	GROUP BY providerspecialty) A
<br>WHERE
<br>	netcharge > 25000
<br>ORDER BY netcollectionrate;

#### Output
<img src="paymentpername.png" alt="paymentpername" style="width:400px;height:300px;">
