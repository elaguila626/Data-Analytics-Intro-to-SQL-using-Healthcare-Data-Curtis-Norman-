# Healthcare Data SQL Practice Questions

### How many rows of data are in the FactTable that include a Gross Charge greater than $100? 
-- Answer: 5513

SELECT count(grosscharge) AS CountOfRows 
<br>FROM facttable
<br>WHERE grosscharge > 100.00;

### How many unique patients exist in the Healthcare_DB?
-- Answer: 4962

SELECT COUNT(DISTINCT(patientnumber)) AS NumberOfUniquePatients 
<br>FROM dimpatient;

### How many CptCodes are in each CptGrouping?
-- Answer: Unique number of CPT codes per department

SELECT cptgrouping, COUNT(DISTINCT(cptcode)) AS CountOfCptCodes
<br> FROM dimcptcode
<br> GROUP BY cptgrouping
<br> ORDER BY 2 DESC;

### How many providers have submitted a Medicare insurance claims?
-- Answer: 682

SELECT COUNT(DISTINCT(providernpi)) AS countofproviders
<br>FROM facttable 
<br>INNER JOIN dimphysician
<br>ON dimphysician.dimphysicianpk = facttable.dimphysicianpk
<br>INNER JOIN dimpayer
<br>ON dimpayer.dimpayerpk = facttable.dimpayerpk
<br>WHERE payername = 'Medicare';

### Calculate the gross collection rate for each location name - see below; GCR = payments divided gross charge. Which locationname has the highest GCR? 

SELECT locationname, 100 *(-SUM(payment)/ sum(grosscharge)) AS GCR_Percent
<br> FROM facttable
<br> INNER JOIN dimlocation
<br> ON dimlocation.dimlocationpk = facttable.dimlocationpk
<br> GROUP BY  locationname
<br> ORDER BY 2 DESC;

### How many CptCodes have more than 100 units?
-- Answer: 29 

SELECT COUNT(*) AS cptwithgreaterthanhundredunits
<br> FROM(
<br>	SELECT cptcode, SUM(cptunits)
<br>	FROM facttable 
<br>	INNER JOIN dimcptcode
<br>	ON dimcptcode.dimcptcodepk = facttable.dimcptcodepk
<br>	GROUP BY cptcode
<br>	HAVING SUM(cptunits) > 100) AS a; 




