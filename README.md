# Pewlett Hackard Retirement and Mentorship Analysis

## Problem Statement

This analysis was completed in response to the need for Pewlett Hackard to plan for a wave of upcoming retirements. The analysis is built on a foundation of internal research that looked into the number of potential near-term retirements, based on a querty that focused on age and hire date. These parameters were used because of trends in retirement that center on age as well as time with a company. The oroginal analysis was primarily coarse in nature, with the primary division being based on company department. 

The original analyisis was conducted using six source csv files. Prior to any data manipulation the connecitons between the tables were charted out as an entity relatinship diagram. See the file ```EmployeeDB.png``` or Figure 1.

![ERD](/EmployeeDB.png)
Figure 1. Entity relationship diagram for the source data.

 The analysis in covered in this document builds on the previous analysis in two ways. First, if provides another way of slicing the data, namely by position type. Second, building off an initiative to assist with the transition of new vacancies of a large swatch of employees, this analysis looks beyond the retirement and identifies the eligible class of candidates for mentorship.

For the company's purposes, knowing the number of employees by position who will be exiting the workforce will allow for more strategic personnel planning whereas knowing how many and which candidates will be eligible for mentorship will assist in allocating resources.

## Analysis Process

This analysis took place in two distinct parts which considered two somewhat distinct sets of data. Analysis begins at line 234 in the accompanying ```queries.sql``` document.

### Part 1. Impending Retirements by Employee Titles

Using the table ```retirment_info```, which had been created in previous analysis meant that the present analysis started with a comprehensive table of current employees who are also impending retirees, as defined by the range of birth dates. This table was joined with ```title```, which is based off of salary information from the table ```salaries``` and employee information found in ```titles.csv```. It is noted that, with the exception of the csv file ```titles.csv``` and the corresponding table ```title```, all other .csv file namess and table names match, if a csv exists. It must be noted further that all output csv files are located in the data directory as the output csv files are viable data that can be used for furher analysis. Because many employees have worked in various roles within the company, there were a number of employees with duplicate entries. This necessitated removing the previous positions as it is only the current positions which will be vacated. This process was completed using ```PARTITION```, which breaks those employees with multiple entries into sub-tables sorted by the ```from_date``` in descending order and then selects the first, or most recent of titles. It is noted here that the instructions provided indicated that the ```PARTITION``` should be based on the columnb ```to_date```, however, instructions were provided to include only ```from_date``` as a column. As the results should be consiste as long as they are derived from either ```to_date``` or ```from_date```, this issue should not change the results. This analysis was stores as the table ```ret_emp_recent_title``` and saved as ```ret_emp_recent_title.csv```.

Two other tables were requested. Both of these tables could be derived from ```ret_emp_recent_title```. The first of these reqested tables indicated the requirement that it be "showing number of [titles] retiring [sic]." The interpretation of the statement was that this was a simple request for the number of unique titles from which there would be impending retirements. What was requested could be presented as an integer but is, instead, being presented as Table 1 and ```count_unique_titles_ret.csv```. Table 1 was created using the ```COUNT``` and ```DISTINCT``` operators.

Table 1.

The other requested table that was derived from ```ret_emp_recent_title``` reflects the number of employees by position who will be eligible for retirement. This table used ```GROUP BY``` on the ```title``` column in conjunction with ```COUNT``` to provide a tally of those titles. The findings are presented in Table 2 and ```count_unique_titles_ret.csv```.

Table 2.

### Part 2. Mentorship Eligibilty

The goal of the second part of the analysis was to provide a table of the employees who would be eligible for mentorship by recent retirees who are to be brought on in a part-time fashion. There were two routes that could be taken to achieve this goal. The first was to join to the table ```employees``` the table ```title``` for the relevant information and the table ```dept_emp``` in order to establish currency of employment. This route was taken but involves the additional step of deduplcating the table of previous titles using the ```PARTITION``` operation. This result is the table ```mentorship_elig_dedup``` and the correspondiong csv file.

The quicker way of completing this operation, which is also included in ```queries.sql``` was to use the ```to_date``` column in the ```title``` table as a proxy for curreny of employment. This works because the title of those employees who are no longer with the company will have a ```to_date``` value that is in the past, whereas the placeholder value for both employees who are working for the company and in current titles ius the date ```9999-01-01```. The result for this method is ```mentorship_elig_1step``` and the corresponding csv file.

Both methods returned 1549 results with no duplicates.

### General Issues

 Aside from the challenge with the ```PARTITION``` statement and the discovery of the one-step method for returning the table of current employees eligible for mentorsihp, most of this analysis went smoothly. It should be noted, however, that the rubrick reuqires the correct usage of ```NOT NULL```, whereas ```NOT NULL``` was not necessary when going from existing tables to manipulated tables. Instead, ```NOT NULL``` was successfully implemented at the beginning of the process, when tables were being created from the original csv files.

### Results and Conclusion

The data processing accomplished in this challenge will assist higher ups in the company with planning for depatures over the following years as well as with the process of hiring replacements. As a large company, it is should not be a surprise that there are 41380 employees born within the prescribed dates that qualfied them as ready for retirement. These employees are primarily engineers and occupy other titles that appear to relatively senior.

Pewlett Hackard will have quite a bit of hiring in the future. It is beneficial that management has considered the mentorship program. Knowing the number of individuals eligible will help with utilizaiton planning as well as planning for the rehiring of retirees as mentors. With only 1549 eligible mentees, however, it seems like management will need to expand eligibily.

In order to devise a more comprehensive transion plan, managaers should look downstream on the promotion pipeline. It will be useful to know how many in-house junior employees are avaiable for promotion. This knowledge will be practically useful in determining the amount of outside vs inside hiring will be needed  .
