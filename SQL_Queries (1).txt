--ASSESSMENT AUTO INSURANCE COMPANY
--Q1
SELECT count(IDpol)AS TOT_Cust_claimed FROM Auto_insurance
WHERE ClaimNb>=1;
SELECT count(IDpol)AS TOT_Cust_claimed FROM Auto_insurance;
-- Q2
ALTER TABLE Auto_insurance add column claim_flag INT;
UPDATE Auto_insurance SET claim_flag = 1
WHERE ClaimNb>0;
UPDATE Auto_insurance SET claim_flag = 0
WHERE ClaimNb<=0;

--Q3
SELECT claim_flag,ROUND(AVG(Exposure),2) AS AVG_EXP_PERIOD
FROM Auto_insurance
GROUP BY claim_flag;

--Q4
SELECT bucket_list,ROUND(CAST(Total_Claim_bucketwise AS FLOAT)/CAST(Total_Claims AS FLOAT)*100,2) AS PERCENT_OF_CLAIMS
FROM(
SELECT bucket_list,Total_Claim_bucketwise,SUM(Total_Claim_bucketwise) OVER()AS Total_Claims
FROM(
SELECT a.bucket_list,SUM(ClaimNb) AS Total_Claim_bucketwise
FROM(
SELECT *,
CASE 
    WHEN Exposure  between 0 and 0.25 THEN 'E1'
	WHEN Exposure  between 0.26 and 0.5 THEN 'E2'
	WHEN Exposure  between 0.51 and 0.75 THEN 'E3'
	ELSE 'E4'
END bucket_list
FROM Auto_insurance) a
GROUP BY a.bucket_list))
GROUP BY bucket_list;


--Q5
SELECT *,ROUND(CAST(Num_of_claims AS Float)/CAST(Num_of_policies AS Float)*100,2) AS percent_of_claim
FROM(
SELECT Region,SUM(ClaimNb) AS Num_of_claims,COUNT(IDpol) AS Num_of_policies
FROM Auto_insurance
GROUP BY Region)
ORDER BY percent_of_claim DESC;

--Q6
SELECT bucket_list,Area,ROUND(CAST(num_of_claim AS FLOAT)/CAST(tot_policies AS FLOAT)*100,2) AS claim_rate
FROM(
SELECT Area,bucket_list,SUM(ClaimNb) AS num_of_claim,COUNT(IDpol) AS tot_policies
FROM(
SELECT * ,
CASE 
    WHEN Exposure  between 0 and 0.25 THEN 'E1'
	WHEN Exposure  between 0.26 and 0.5 THEN 'E2'
	WHEN Exposure  between 0.51 and 0.75 THEN 'E3'
	ELSE 'E4'
END bucket_list
FROM Auto_insurance)
GROUP BY Area,bucket_list)
GROUP BY bucket_list,Area
ORDER BY Area,bucket_list;

--Q7
SELECT claim_flag,ROUND(AVG(VehAge),2)
FROM Auto_insurance
GROUP BY claim_flag;
--PART 2
SELECT Area,AVG(VehAge) AS veh_age
FROM Auto_insurance
WHERE claim_flag = 1
GROUP BY Area
ORDER BY Area;

--Q8
SELECT bucket_list,ROUND(AVG(VehAge),2) AS veh_age_claimed
FROM (
SELECT * ,
CASE 
    WHEN Exposure  between 0 and 0.25 THEN 'E1'
	WHEN Exposure  between 0.26 and 0.5 THEN 'E2'
	WHEN Exposure  between 0.51 and 0.75 THEN 'E3'
	ELSE 'E4'
END bucket_list
FROM Auto_insurance)
WHERE claim_flag = 1
GROUP BY bucket_list
ORDER BY bucket_list;

SELECT bucket_list,ROUND(AVG(VehAge),2) AS veh_age_notclaimed
FROM (
SELECT * ,
CASE 
    WHEN Exposure  between 0 and 0.25 THEN 'E1'
	WHEN Exposure  between 0.26 and 0.5 THEN 'E2'
	WHEN Exposure  between 0.51 and 0.75 THEN 'E3'
	ELSE 'E4'
END bucket_list
FROM Auto_insurance)
WHERE claim_flag = 0
GROUP BY bucket_list
ORDER BY bucket_list;

--Q9
SELECT Claim_Ct,ROUND(AVG(BonusMalus),2) AS avg_bonusmalus FROM (
SELECT * ,
CASE 
    WHEN ClaimNb = 1 THEN '1 Claim'
	WHEN ClaimNb > 1 THEN 'MT1 Claim'
	ELSE 'No Claim'
END Claim_Ct
FROM Auto_insurance)
GROUP BY Claim_Ct;

--Q10
SELECT Claim_Ct,ROUND(AVG(Density),2) AS avg_density FROM (
SELECT * ,
CASE 
    WHEN ClaimNb = 1 THEN '1 Claim'
	WHEN ClaimNb > 1 THEN 'MT1 Claim'
	ELSE 'No Claim'
END Claim_Ct
FROM Auto_insurance)
GROUP BY Claim_Ct;

--Q11
SELECT VehBrand,VehGas,round(AVG(ClaimNb),3) AS avg_claim
FROM Auto_insurance
GROUP BY VehGas,VehBrand
ORDER BY avg_claim DESC;

--Q12
SELECT Region,bucket_list,SUM(claim_flag) AS claims
FROM(
SELECT *,CASE 
    WHEN Exposure  between 0 and 0.25 THEN 'E1'
	WHEN Exposure  between 0.26 and 0.5 THEN 'E2'
	WHEN Exposure  between 0.51 and 0.75 THEN 'E3'
	ELSE 'E4'
END bucket_list
FROM Auto_insurance)
GROUP BY bucket_list,Region
ORDER BY claims DESC
LIMIT 5;

--Q13
SELECT Age_flag,ROUND(Avg(BonusMalus),2) as avg_bonusmalus
FROM(
SELECT * ,
CASE 
WHEN DrivAge=18 then 'Beginner'
WHEN DrivAge BETWEEN 19 AND 30 then 'Junior'
WHEN DrivAge BETWEEN 31 AND 45 then 'Middle Age'
WHEN DrivAge BETWEEN 46 AND 60 then 'Mid-Senior'
WHEN DrivAge >60 then 'Senior'
ELSE 'Illegal'
END AS Age_flag
FROM Auto_insurance)
GROUP BY Age_flag
ORDER BY avg_bonusmalus DESC;