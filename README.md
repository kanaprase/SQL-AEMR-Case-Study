# SQL-AEMR-Case-Study

# AEMR Part 1, 1.1 - Calculates the average duration in days for each valid outage type and compares the average durations for Start Time in 2016 and 2017
SELECT status AS Status, reason AS Reason, COUNT(*) AS Total_Number_Outage_Events, ROUND(AVG(TIMESTAMPDIFF(minute,start_time, end_time)/1440), 2) AS Average_Outage_Duration_Time_Days, year(start_time) AS Year
FROM AEMR
WHERE status = 'Approved'
GROUP BY year, reason
ORDER BY year, reason

# AEMR Part 1, 1.2 - Shows the monthly count of all approved outage types that occurred for 2016.
SELECT status AS Status, reason AS Reason, COUNT(*) AS Total_Number_Outage_Events, MONTH(start_time) AS Month
FROM AEMR
WHERE YEAR(start_time) = '2016' AND status = 'Approved'
GROUP BY reason, month
ORDER BY reason, month

#AEMR Part 2, 1.1 - Calculate the proportion of outages that were forced for both 2016 and 2017.
SELECT SUM(CASE WHEN reason='Forced' THEN 1 ELSE 0 END) AS Total_Number_Forced_Outage_Events,
COUNT(*) AS Total_Number_Outage_Events, ROUND(((SUM(CASE WHEN reason='Forced' THEN 1 ELSE 0 END)/COUNT(*))*100),2) AS Forced_Outage_Percentage,
YEAR(start_time) AS Year
FROM AEMR
WHERE status = 'Approved'
GROUP BY year
ORDER BY year

#AEMR Part 2, 1.2 - Calculate the AVERAGE duration and AVERAGE energy lost (MW) of all valid forced outages for each participant code
SELECT participant_code AS Participant_Code, status AS Status, year(start_time) AS Year,
ROUND(AVG(outage_mw),2) AS Avg_Outage_MW_Loss,
ROUND(AVG(TIMESTAMPDIFF(minute,start_time,end_time)/1440),2) AS Average_Outage_Duration_Time_Minutes 
FROM AEMR
WHERE status = 'Approved' AND reason = 'Forced'
GROUP BY participant_code, status, year
ORDER BY year, Avg_Outage_MW_Loss DESC

#AEMR Part 2, 1.3 - Aggregate all relevant data together
SELECT participant_code AS Participant_Code, reason, month(start_time) AS Month, year(start_time) AS Year, 
ROUND(AVG(outage_mw),2) AS Avg_Outage_MW_Loss, ROUND(SUM(outage_mw),2) AS Summed_Energy_Lost, COUNT(*) AS Total_Number_Outage_Events
FROM AEMR
WHERE status = 'Approved'
GROUP BY participant_code, reason, year, month
ORDER BY participant_code, reason, year, month

