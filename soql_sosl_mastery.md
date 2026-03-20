# SOQL & SOSL Mastery Guide
## 20 Scenarios Per Concept (Easy to Ultra-Complex)

---

## TABLE OF CONTENTS
1. [SELECT Clause](#select-clause)
2. [WHERE Clause](#where-clause)
3. [Relationship Queries](#relationship-queries)
4. [Aggregate Functions](#aggregate-functions)
5. [Date & Time Functions](#date-time-functions)
6. [ORDER BY & LIMIT](#order-by-limit)
7. [LIKE Operator](#like-operator)
8. [IN & NOT IN Operators](#in-not-in-operators)
9. [NULL Handling](#null-handling)
10. [GROUP BY & HAVING](#group-by-having)
11. [Semi-Joins & Anti-Joins](#semi-joins-anti-joins)
12. [SOSL Searches](#sosl-searches)

---

## SELECT CLAUSE

### Scenario 1: Basic Single Field (Easy)
**Requirement:** Get all account names
```sql
SELECT Name FROM Account
```

### Scenario 2: Multiple Fields (Easy)
**Requirement:** Get contact's first name, last name, and email
```sql
SELECT FirstName, LastName, Email FROM Contact
```

### Scenario 3: All Standard Fields (Easy-Medium)
**Requirement:** Get all important lead fields
```sql
SELECT Id, Name, Company, Email, Phone, Status, LeadSource, CreatedDate FROM Lead
```

### Scenario 4: Select with Alias (Medium)
**Requirement:** Get opportunity name with a custom column name
```sql
SELECT Name OpportunityName, Amount DealValue FROM Opportunity
```

### Scenario 5: Select Formula Fields (Medium)
**Requirement:** Get accounts with their formula field values
```sql
SELECT Name, AnnualRevenue, Industry, CustomFormula__c FROM Account
```

### Scenario 6: Select Parent Fields (Medium)
**Requirement:** Get contact name along with their account name
```sql
SELECT FirstName, LastName, Account.Name FROM Contact
```

### Scenario 7: Multi-Level Parent Fields (Medium-Hard)
**Requirement:** Get opportunity with account owner information
```sql
SELECT Name, Amount, Account.Name, Account.Owner.Name, Account.Owner.Email FROM Opportunity
```

### Scenario 8: Select with Type Conversion (Medium-Hard)
**Requirement:** Get opportunities with converted amount format
```sql
SELECT Name, FORMAT(Amount) FormattedAmount, CloseDate FROM Opportunity
```

### Scenario 9: Select Currency Fields Multi-Currency (Hard)
**Requirement:** Get opportunities with different currency conversions
```sql
SELECT Name, Amount, CurrencyIsoCode, 
       CONVERTCURRENCY(Amount) ConvertedAmount 
FROM Opportunity
```

### Scenario 10: Select Polymorphic Fields (Hard)
**Requirement:** Get task with polymorphic relationship (Who and What)
```sql
SELECT Id, Subject, Who.Name, Who.Type, What.Name, What.Type FROM Task
```

### Scenario 11: Select Geolocation Fields (Hard)
**Requirement:** Get accounts with geolocation data
```sql
SELECT Name, BillingAddress, BillingLatitude, BillingLongitude,
       DISTANCE(BillingAddress, GEOLOCATION(37.7749, -122.4194), 'mi') Distance
FROM Account
```

### Scenario 12: Select with TYPEOF for Polymorphic (Hard)
**Requirement:** Get events with type-specific fields
```sql
SELECT Id, Subject,
       TYPEOF What
           WHEN Account THEN Phone, Industry
           WHEN Opportunity THEN Amount, StageName
           ELSE Name
       END
FROM Event
```

### Scenario 13: Select Picklist API and Label (Medium-Hard)
**Requirement:** Get both API value and label for picklist
```sql
SELECT Name, StageName, TOLABEL(StageName) StageLabel FROM Opportunity
```

### Scenario 14: Select with Field-Level Security Check (Hard)
**Requirement:** Get fields respecting user's FLS (Apex context)
```sql
-- In Apex with WITH SECURITY_ENFORCED
SELECT Id, Name, SSN__c FROM Contact WITH SECURITY_ENFORCED
```

### Scenario 15: Select Lookup Relationship Null Handling (Medium-Hard)
**Requirement:** Get contacts even if account is null
```sql
SELECT Id, Name, Account.Name, Account.Industry FROM Contact
```

### Scenario 16: Select Rollup Summary Fields (Medium)
**Requirement:** Get accounts with rollup summary of opportunities
```sql
SELECT Name, NumberOfEmployees, TotalOpportunityAmount__c FROM Account
```

### Scenario 17: Select RecordType Information (Medium-Hard)
**Requirement:** Get opportunities with RecordType details
```sql
SELECT Name, Amount, RecordType.Name, RecordType.DeveloperName FROM Opportunity
```

### Scenario 18: Select Custom Metadata (Hard)
**Requirement:** Query custom metadata type
```sql
SELECT Label, DeveloperName, CustomField__c FROM CustomMetadata__mdt
```

### Scenario 19: Select External Objects (Hard)
**Requirement:** Query external object via External Data Source
```sql
SELECT ExternalId, Name, Status__c FROM ExternalObject__x
```

### Scenario 20: Select with Row Locking (Ultra-Complex)
**Requirement:** Lock records for update to prevent concurrent modification
```sql
SELECT Id, Name, Amount FROM Opportunity 
WHERE StageName = 'Negotiation' 
FOR UPDATE
```

---

## WHERE CLAUSE

### Scenario 1: Simple Equality (Easy)
**Requirement:** Find contacts with specific last name
```sql
SELECT Name FROM Contact WHERE LastName = 'Smith'
```

### Scenario 2: Not Equal (Easy)
**Requirement:** Find all opportunities not in prospecting stage
```sql
SELECT Name FROM Opportunity WHERE StageName != 'Prospecting'
```

### Scenario 3: Greater Than (Easy)
**Requirement:** Find opportunities greater than $50,000
```sql
SELECT Name, Amount FROM Opportunity WHERE Amount > 50000
```

### Scenario 4: Less Than or Equal (Easy)
**Requirement:** Find accounts with employees less than or equal to 100
```sql
SELECT Name FROM Account WHERE NumberOfEmployees <= 100
```

### Scenario 5: Multiple AND Conditions (Medium)
**Requirement:** Find high-value technology opportunities
```sql
SELECT Name, Amount FROM Opportunity 
WHERE Amount > 100000 
AND StageName = 'Proposal/Price Quote' 
AND Account.Industry = 'Technology'
```

### Scenario 6: OR Conditions (Medium)
**Requirement:** Find contacts from California or New York
```sql
SELECT Name, MailingState FROM Contact 
WHERE MailingState = 'CA' OR MailingState = 'NY'
```

### Scenario 7: Combined AND/OR with Parentheses (Medium)
**Requirement:** Find specific leads with complex criteria
```sql
SELECT Name, Status FROM Lead 
WHERE (Status = 'Open' OR Status = 'Working') 
AND (LeadSource = 'Web' OR LeadSource = 'Phone Inquiry')
AND Rating = 'Hot'
```

### Scenario 8: WHERE with Parent Field (Medium)
**Requirement:** Find contacts where account annual revenue exceeds threshold
```sql
SELECT Name, Account.Name FROM Contact 
WHERE Account.AnnualRevenue > 1000000
```

### Scenario 9: WHERE with Formula Field (Medium-Hard)
**Requirement:** Filter by custom formula field result
```sql
SELECT Name, DaysToClose__c FROM Opportunity 
WHERE DaysToClose__c < 30
```

### Scenario 10: WHERE with Multiple Parent Levels (Medium-Hard)
**Requirement:** Find opportunities based on account owner's role
```sql
SELECT Name, Account.Name FROM Opportunity 
WHERE Account.Owner.UserRole.Name = 'VP Sales'
```

### Scenario 11: WHERE with RecordType (Medium)
**Requirement:** Find specific record type opportunities
```sql
SELECT Name FROM Opportunity 
WHERE RecordType.DeveloperName = 'Enterprise_Deal'
```

### Scenario 12: WHERE with Boolean Field (Easy-Medium)
**Requirement:** Find only converted leads
```sql
SELECT Name, Company FROM Lead WHERE IsConverted = true
```

### Scenario 13: WHERE with Checkbox Combinations (Medium)
**Requirement:** Find accounts with specific boolean criteria
```sql
SELECT Name FROM Account 
WHERE IsPartner = true 
AND IsCustomer = false 
AND Active__c = true
```

### Scenario 14: WHERE with Currency Comparison (Medium)
**Requirement:** Find opportunities in specific currency range
```sql
SELECT Name, Amount, CurrencyIsoCode FROM Opportunity 
WHERE Amount > 50000 
AND CurrencyIsoCode = 'USD'
```

### Scenario 15: WHERE with INCLUDES (Multi-Select Picklist) (Hard)
**Requirement:** Find accounts with specific multi-select picklist values
```sql
SELECT Name FROM Account 
WHERE Products_Interested__c INCLUDES ('Product A', 'Product B')
```

### Scenario 16: WHERE with EXCLUDES (Multi-Select Picklist) (Hard)
**Requirement:** Find accounts excluding certain products
```sql
SELECT Name FROM Account 
WHERE Products_Interested__c EXCLUDES ('Product C', 'Product D')
```

### Scenario 17: WHERE with Complex Nested Conditions (Hard)
**Requirement:** Multi-level filtering with various operators
```sql
SELECT Name, Amount, StageName FROM Opportunity 
WHERE (
    (Amount > 100000 AND StageName IN ('Proposal/Price Quote', 'Negotiation/Review'))
    OR 
    (Amount > 500000 AND StageName = 'Prospecting')
) 
AND Account.Industry IN ('Technology', 'Finance', 'Healthcare')
AND CloseDate = THIS_QUARTER
AND IsClosed = false
```

### Scenario 18: WHERE with Owner Filtering (Medium-Hard)
**Requirement:** Find records owned by specific users
```sql
SELECT Name FROM Opportunity 
WHERE Owner.Profile.Name = 'Sales Representative'
AND Owner.IsActive = true
```

### Scenario 19: WHERE with System Fields (Medium)
**Requirement:** Filter by system modification fields
```sql
SELECT Name FROM Account 
WHERE CreatedBy.Name = 'Integration User'
AND LastModifiedDate > 2024-01-01T00:00:00Z
```

### Scenario 20: WHERE with Division (Ultra-Complex - if divisions enabled)
**Requirement:** Query specific division records
```sql
SELECT Name, Division FROM Account 
WHERE Division = 'North America'
AND AnnualRevenue > 1000000
```

---

## RELATIONSHIP QUERIES

### Scenario 1: Simple Parent-to-Child (Easy)
**Requirement:** Get accounts with their contacts
```sql
SELECT Name, (SELECT FirstName, LastName FROM Contacts) FROM Account
```

### Scenario 2: Parent-to-Child with WHERE (Medium)
**Requirement:** Get accounts with only active contacts
```sql
SELECT Name, 
       (SELECT FirstName, LastName FROM Contacts WHERE Active__c = true) 
FROM Account
```

### Scenario 3: Multiple Child Relationships (Medium)
**Requirement:** Get accounts with contacts and opportunities
```sql
SELECT Name, 
       (SELECT FirstName, LastName FROM Contacts),
       (SELECT Name, Amount FROM Opportunities)
FROM Account
```

### Scenario 4: Child-to-Parent Simple (Easy)
**Requirement:** Get opportunities with account name
```sql
SELECT Name, Amount, Account.Name FROM Opportunity
```

### Scenario 5: Child-to-Parent Multi-Level (Medium)
**Requirement:** Get opportunities with account and owner details
```sql
SELECT Name, Amount, 
       Account.Name, Account.Industry,
       Owner.Name, Owner.Email, Owner.Profile.Name
FROM Opportunity
```

### Scenario 6: Child Relationship with ORDER BY (Medium)
**Requirement:** Get accounts with contacts ordered by name
```sql
SELECT Name, 
       (SELECT FirstName, LastName FROM Contacts ORDER BY LastName) 
FROM Account
```

### Scenario 7: Child Relationship with LIMIT (Medium)
**Requirement:** Get top 5 opportunities per account
```sql
SELECT Name, 
       (SELECT Name, Amount FROM Opportunities 
        ORDER BY Amount DESC LIMIT 5)
FROM Account
```

### Scenario 8: Multiple Levels - 3 Deep (Hard)
**Requirement:** Navigate three levels of relationships
```sql
SELECT Name, 
       Account.Name, 
       Account.Owner.Name,
       Account.Owner.Manager.Name
FROM Opportunity
```

### Scenario 9: Custom Object Relationships (Medium)
**Requirement:** Query custom object with parent
```sql
SELECT Name, CustomField__c, 
       ParentCustomObject__r.Name,
       ParentCustomObject__r.Status__c
FROM ChildCustomObject__c
```

### Scenario 10: Cross-Object Formula Field (Medium-Hard)
**Requirement:** Query using relationship formula
```sql
SELECT Name, 
       Account.AnnualRevenue,
       Account.OpportunityTotalValue__c
FROM Contact
WHERE Account.OpportunityTotalValue__c > 500000
```

### Scenario 11: Polymorphic Relationship - Task (Hard)
**Requirement:** Get tasks with Who and What relationships
```sql
SELECT Subject, 
       Who.Name, 
       What.Name,
       Owner.Name
FROM Task
WHERE Who.Type = 'Contact'
```

### Scenario 12: Self-Referencing Relationship (Hard)
**Requirement:** Query account hierarchy
```sql
SELECT Name, 
       Parent.Name,
       Parent.Parent.Name
FROM Account
WHERE Parent.Name != null
```

### Scenario 13: Junction Object Query (Hard)
**Requirement:** Query many-to-many relationship through junction
```sql
SELECT Name,
       Project__r.Name,
       Employee__r.Name,
       Role__c,
       Hours__c
FROM ProjectAssignment__c
WHERE Project__r.Status__c = 'Active'
```

### Scenario 14: Child Relationship with Aggregates (Hard)
**Requirement:** Get accounts with aggregate child data (Note: not directly supported, needs subquery)
```sql
SELECT Name,
       (SELECT COUNT() FROM Opportunities WHERE IsClosed = false) OpenOpps
FROM Account
```

### Scenario 15: External Lookup Relationship (Hard)
**Requirement:** Query external object relationship
```sql
SELECT Name,
       ExternalObject__r.ExternalId,
       ExternalObject__r.Status__c
FROM CustomObject__c
WHERE ExternalObject__r.Status__c = 'Active'
```

### Scenario 16: Relationship with RecordType (Medium-Hard)
**Requirement:** Filter by related record's RecordType
```sql
SELECT Name, Amount,
       Account.Name,
       Account.RecordType.Name
FROM Opportunity
WHERE Account.RecordType.DeveloperName = 'Enterprise_Account'
```

### Scenario 17: Master-Detail with Sharing (Hard)
**Requirement:** Query master-detail considering sharing
```sql
SELECT Name,
       MasterObject__r.Name,
       MasterObject__r.Owner.Name
FROM DetailObject__c
WHERE MasterObject__r.Status__c = 'Approved'
```

### Scenario 18: Nested Child Queries with Filters (Hard)
**Requirement:** Complex parent-child with multiple filters
```sql
SELECT Name, Industry,
       (SELECT Name, Amount, StageName FROM Opportunities 
        WHERE Amount > 50000 
        AND StageName NOT IN ('Closed Won', 'Closed Lost')
        ORDER BY CloseDate),
       (SELECT FirstName, LastName, Email FROM Contacts
        WHERE Email != null
        AND DoNotCall = false
        ORDER BY LastName
        LIMIT 10)
FROM Account
WHERE Industry = 'Technology'
AND AnnualRevenue > 1000000
```

### Scenario 19: Relationship Query with TYPEOF (Ultra-Hard)
**Requirement:** Query polymorphic with type-specific fields
```sql
SELECT Id, Subject,
       TYPEOF Who
           WHEN Contact THEN FirstName, LastName, Account.Name
           WHEN Lead THEN Company, Status
           ELSE Name
       END
FROM Task
WHERE ActivityDate = TODAY
```

### Scenario 20: Complex Multi-Object Relationship Chain (Ultra-Complex)
**Requirement:** Navigate complex relationship hierarchy
```sql
SELECT Name,
       Contact.Account.Name,
       Contact.Account.Owner.Manager.Name,
       Contact.Account.Owner.Manager.UserRole.Name,
       Asset.Product2.ProductCode,
       Asset.Product2.Family
FROM Case
WHERE Contact.Account.Industry = 'Healthcare'
AND Asset.Status = 'Installed'
AND CreatedDate = THIS_MONTH
```

---

## AGGREGATE FUNCTIONS

### Scenario 1: Simple COUNT (Easy)
**Requirement:** Count all opportunities
```sql
SELECT COUNT() FROM Opportunity
```

### Scenario 2: COUNT with Field (Easy)
**Requirement:** Count opportunities with amount populated
```sql
SELECT COUNT(Id) FROM Opportunity WHERE Amount != null
```

### Scenario 3: SUM Function (Easy)
**Requirement:** Calculate total opportunity value
```sql
SELECT SUM(Amount) FROM Opportunity
```

### Scenario 4: AVG Function (Easy)
**Requirement:** Calculate average opportunity amount
```sql
SELECT AVG(Amount) FROM Opportunity
```

### Scenario 5: MIN and MAX (Easy)
**Requirement:** Find smallest and largest opportunity
```sql
SELECT MIN(Amount), MAX(Amount) FROM Opportunity
```

### Scenario 6: COUNT with WHERE (Medium)
**Requirement:** Count won opportunities this year
```sql
SELECT COUNT() FROM Opportunity 
WHERE StageName = 'Closed Won' 
AND CloseDate = THIS_YEAR
```

### Scenario 7: Multiple Aggregates (Medium)
**Requirement:** Get comprehensive opportunity statistics
```sql
SELECT COUNT(Id) TotalOpps,
       SUM(Amount) TotalValue,
       AVG(Amount) AvgValue,
       MIN(Amount) MinValue,
       MAX(Amount) MaxValue
FROM Opportunity
WHERE CloseDate = THIS_YEAR
```

### Scenario 8: COUNT_DISTINCT (Medium)
**Requirement:** Count unique account industries
```sql
SELECT COUNT_DISTINCT(Industry) FROM Account
```

### Scenario 9: Aggregate with Alias (Medium)
**Requirement:** Label aggregate results
```sql
SELECT SUM(Amount) TotalRevenue,
       AVG(Amount) AverageRevenue,
       COUNT() TotalDeals
FROM Opportunity
WHERE StageName = 'Closed Won'
```

### Scenario 10: Aggregate on Date Field (Medium-Hard)
**Requirement:** Find earliest and latest close dates
```sql
SELECT MIN(CloseDate) EarliestClose,
       MAX(CloseDate) LatestClose
FROM Opportunity
WHERE StageName = 'Closed Won'
```

### Scenario 11: Aggregate with Relationship (Hard)
**Requirement:** Sum amounts by account industry
```sql
SELECT Account.Industry,
       SUM(Amount) TotalRevenue
FROM Opportunity
WHERE StageName = 'Closed Won'
GROUP BY Account.Industry
```

### Scenario 12: COUNT with Multiple Conditions (Medium)
**Requirement:** Count high-value technology deals
```sql
SELECT COUNT() 
FROM Opportunity 
WHERE Amount > 100000 
AND Account.Industry = 'Technology'
AND CloseDate = THIS_QUARTER
```

### Scenario 13: Aggregate on Formula Field (Medium-Hard)
**Requirement:** Average days to close
```sql
SELECT AVG(DaysToClose__c) AvgDaysToClose
FROM Opportunity
WHERE StageName = 'Closed Won'
AND CloseDate = THIS_YEAR
```

### Scenario 14: Aggregate with Currency Conversion (Hard)
**Requirement:** Sum opportunities with currency conversion
```sql
SELECT SUM(CONVERTCURRENCY(Amount)) TotalInCorporateCurrency
FROM Opportunity
WHERE CloseDate = THIS_YEAR
```

### Scenario 15: Nested Aggregates Simulation (Hard)
**Requirement:** Calculate percentage of won deals
```sql
-- First query
SELECT COUNT() TotalOpps FROM Opportunity WHERE CloseDate = THIS_YEAR
-- Second query
SELECT COUNT() WonOpps FROM Opportunity 
WHERE StageName = 'Closed Won' AND CloseDate = THIS_YEAR
-- Calculate percentage in Apex
```

### Scenario 16: Aggregate with CALENDAR Functions (Hard)
**Requirement:** Sum by calendar month
```sql
SELECT CALENDAR_MONTH(CloseDate) Month,
       SUM(Amount) MonthlyRevenue
FROM Opportunity
WHERE CloseDate = THIS_YEAR
GROUP BY CALENDAR_MONTH(CloseDate)
ORDER BY CALENDAR_MONTH(CloseDate)
```

### Scenario 17: Aggregate on Multi-Currency (Hard)
**Requirement:** Sum by currency type
```sql
SELECT CurrencyIsoCode,
       SUM(Amount) TotalByCurrency,
       COUNT(Id) OpportunityCount
FROM Opportunity
WHERE CloseDate = THIS_YEAR
GROUP BY CurrencyIsoCode
```

### Scenario 18: Complex Aggregate with Multiple Grouping (Ultra-Hard)
**Requirement:** Revenue analysis by multiple dimensions
```sql
SELECT Account.Industry,
       Owner.Name,
       CALENDAR_QUARTER(CloseDate) Quarter,
       SUM(Amount) Revenue,
       AVG(Amount) AvgDeal,
       COUNT(Id) DealCount
FROM Opportunity
WHERE CloseDate = THIS_YEAR
AND StageName = 'Closed Won'
GROUP BY Account.Industry, Owner.Name, CALENDAR_QUARTER(CloseDate)
ORDER BY SUM(Amount) DESC
```

### Scenario 19: Aggregate with HAVING Multiple Conditions (Ultra-Hard)
**Requirement:** Find high-performing accounts
```sql
SELECT Account.Name,
       COUNT(Id) OpportunityCount,
       SUM(Amount) TotalRevenue,
       AVG(Amount) AvgDealSize
FROM Opportunity
WHERE StageName = 'Closed Won'
AND CloseDate = THIS_YEAR
GROUP BY Account.Name
HAVING COUNT(Id) > 5
AND SUM(Amount) > 500000
AND AVG(Amount) > 50000
ORDER BY SUM(Amount) DESC
```

### Scenario 20: Aggregate with ROLLUP Simulation (Ultra-Complex)
**Requirement:** Hierarchical aggregation (requires multiple queries)
```sql
-- Query 1: Grand total
SELECT SUM(Amount) GrandTotal FROM Opportunity WHERE CloseDate = THIS_YEAR

-- Query 2: By Industry
SELECT Account.Industry, SUM(Amount) IndustryTotal 
FROM Opportunity WHERE CloseDate = THIS_YEAR GROUP BY Account.Industry

-- Query 3: By Industry and Owner
SELECT Account.Industry, Owner.Name, SUM(Amount) 
FROM Opportunity WHERE CloseDate = THIS_YEAR 
GROUP BY Account.Industry, Owner.Name

-- Combine in Apex for ROLLUP effect
```

---

## DATE & TIME FUNCTIONS

### Scenario 1: TODAY Function (Easy)
**Requirement:** Find opportunities closing today
```sql
SELECT Name FROM Opportunity WHERE CloseDate = TODAY
```

### Scenario 2: THIS_WEEK (Easy)
**Requirement:** Find leads created this week
```sql
SELECT Name FROM Lead WHERE CreatedDate = THIS_WEEK
```

### Scenario 3: THIS_MONTH (Easy)
**Requirement:** Find cases opened this month
```sql
SELECT CaseNumber FROM Case WHERE CreatedDate = THIS_MONTH
```

### Scenario 4: LAST_MONTH (Easy)
**Requirement:** Find opportunities closed last month
```sql
SELECT Name FROM Opportunity WHERE CloseDate = LAST_MONTH
```

### Scenario 5: THIS_QUARTER (Medium)
**Requirement:** Find deals closing this quarter
```sql
SELECT Name, Amount FROM Opportunity WHERE CloseDate = THIS_QUARTER
```

### Scenario 6: LAST_QUARTER (Medium)
**Requirement:** Analyze previous quarter performance
```sql
SELECT Name, Amount FROM Opportunity 
WHERE CloseDate = LAST_QUARTER
AND StageName = 'Closed Won'
```

### Scenario 7: THIS_YEAR (Easy)
**Requirement:** Find all opportunities this year
```sql
SELECT Name FROM Opportunity WHERE CloseDate = THIS_YEAR
```

### Scenario 8: LAST_N_DAYS (Medium)
**Requirement:** Find recently modified accounts
```sql
SELECT Name FROM Account WHERE LastModifiedDate = LAST_N_DAYS:30
```

### Scenario 9: NEXT_N_DAYS (Medium)
**Requirement:** Find upcoming opportunity closes
```sql
SELECT Name, CloseDate FROM Opportunity 
WHERE CloseDate = NEXT_N_DAYS:7
AND IsClosed = false
```

### Scenario 10: Date Range with Specific Dates (Medium)
**Requirement:** Find opportunities in specific date range
```sql
SELECT Name FROM Opportunity 
WHERE CloseDate >= 2024-01-01 
AND CloseDate <= 2024-12-31
```

### Scenario 11: LAST_N_WEEKS (Medium)
**Requirement:** Find leads from last 4 weeks
```sql
SELECT Name, Company FROM Lead WHERE CreatedDate = LAST_N_WEEKS:4
```

### Scenario 12: NEXT_N_WEEKS (Medium)
**Requirement:** Find upcoming renewals
```sql
SELECT Name, RenewalDate__c FROM Contract__c 
WHERE RenewalDate__c = NEXT_N_WEEKS:2
```

### Scenario 13: LAST_N_MONTHS (Medium)
**Requirement:** Find customer activity in last 3 months
```sql
SELECT Name FROM Account WHERE LastActivityDate = LAST_N_MONTHS:3
```

### Scenario 14: NEXT_N_MONTHS (Medium)
**Requirement:** Find future scheduled events
```sql
SELECT Subject FROM Event WHERE ActivityDate = NEXT_N_MONTHS:2
```

### Scenario 15: THIS_FISCAL_QUARTER (Medium-Hard)
**Requirement:** Current fiscal quarter deals
```sql
SELECT Name, Amount FROM Opportunity 
WHERE CloseDate = THIS_FISCAL_QUARTER
```

### Scenario 16: LAST_FISCAL_QUARTER (Medium-Hard)
**Requirement:** Previous fiscal quarter analysis
```sql
SELECT Account.Name, SUM(Amount) 
FROM Opportunity
WHERE CloseDate = LAST_FISCAL_QUARTER
AND StageName = 'Closed Won'
GROUP BY Account.Name
```

### Scenario 17: THIS_FISCAL_YEAR (Medium)
**Requirement:** Current fiscal year opportunities
```sql
SELECT Name FROM Opportunity WHERE CloseDate = THIS_FISCAL_YEAR
```

### Scenario 18: YESTERDAY and TOMORROW (Easy)
**Requirement:** Find yesterday's activities
```sql
SELECT Subject FROM Task WHERE ActivityDate = YESTERDAY

SELECT Subject FROM Event WHERE ActivityDate = TOMORROW
```

### Scenario 19: CALENDAR_MONTH and CALENDAR_YEAR (Hard)
**Requirement:** Group opportunities by month and year
```sql
SELECT CALENDAR_MONTH(CloseDate) Month,
       CALENDAR_YEAR(CloseDate) Year,
       SUM(Amount) Revenue
FROM Opportunity
WHERE CloseDate = LAST_N_YEARS:2
GROUP BY CALENDAR_MONTH(CloseDate), CALENDAR_YEAR(CloseDate)
ORDER BY CALENDAR_YEAR(CloseDate), CALENDAR_MONTH(CloseDate)
```

### Scenario 20: Complex Date Logic with Multiple Periods (Ultra-Complex)
**Requirement:** Compare performance across multiple time periods
```sql
-- Current Quarter
SELECT 'Current Quarter' Period,
       COUNT(Id) Opportunities,
       SUM(Amount) Revenue
FROM Opportunity
WHERE CloseDate = THIS_QUARTER
AND StageName = 'Closed Won'

UNION ALL

-- Last Quarter (requires separate query in SOQL)
SELECT 'Last Quarter' Period,
       COUNT(Id) Opportunities,
       SUM(Amount) Revenue
FROM Opportunity
WHERE CloseDate = LAST_QUARTER
AND StageName = 'Closed Won'

-- Note: UNION not supported in SOQL, use Apex to combine
```

---

## ORDER BY & LIMIT

### Scenario 1: Simple ORDER BY ASC (Easy)
**Requirement:** Sort accounts alphabetically
```sql
SELECT Name FROM Account ORDER BY Name ASC
```

### Scenario 2: Simple ORDER BY DESC (Easy)
**Requirement:** Sort opportunities by amount (highest first)
```sql
SELECT Name, Amount FROM Opportunity ORDER BY Amount DESC
```

### Scenario 3: ORDER BY with LIMIT (Easy)
**Requirement:** Get top 10 opportunities
```sql
SELECT Name, Amount FROM Opportunity 
ORDER BY Amount DESC 
LIMIT 10
```

### Scenario 4: ORDER BY Multiple Fields (Medium)
**Requirement:** Sort by stage, then by amount
```sql
SELECT Name, StageName, Amount FROM Opportunity 
ORDER BY StageName ASC, Amount DESC
```

### Scenario 5: ORDER BY with NULL Values (Medium)
**Requirement:** Sort accounts, nulls last
```sql
SELECT Name, AnnualRevenue FROM Account 
ORDER BY AnnualRevenue DESC NULLS LAST
```

### Scenario 6: ORDER BY NULLS FIRST (Medium)
**Requirement:** Show accounts without revenue first
```sql
SELECT Name, AnnualRevenue FROM Account 
ORDER BY AnnualRevenue ASC NULLS FIRST
```

### Scenario 7: ORDER BY Date Field (Medium)
**Requirement:** Sort opportunities by close date
```sql
SELECT Name, CloseDate FROM Opportunity 
ORDER BY CloseDate ASC
```

### Scenario 8: ORDER BY with Parent Field (Medium)
**Requirement:** Sort contacts by account name
```sql
SELECT FirstName, LastName, Account.Name FROM Contact 
ORDER BY Account.Name ASC
```

### Scenario 9: ORDER BY Formula Field (Medium-Hard)
**Requirement:** Sort by calculated field
```sql
SELECT Name, DaysOpen__c FROM Case 
ORDER BY DaysOpen__c DESC
```

### Scenario 10: LIMIT with OFFSET (Medium)
**Requirement:** Implement pagination (skip first 100)
```sql
SELECT Name FROM Account 
ORDER BY Name 
LIMIT 50 
OFFSET 100
```

### Scenario 11: ORDER BY with Multiple Directions (Medium)
**Requirement:** Complex sorting criteria
```sql
SELECT Name, StageName, Amount, CloseDate FROM Opportunity 
ORDER BY StageName ASC, Amount DESC, CloseDate ASC
```

### Scenario 12: ORDER BY with Relationship Fields (Medium-Hard)
**Requirement:** Sort by multiple relationship levels
```sql
SELECT Name, Account.Name, Account.Owner.Name FROM Opportunity 
ORDER BY Account.Owner.Name ASC, Account.Name ASC, Name ASC
```

### Scenario 13: ORDER BY on Aggregate Query (Hard)
**Requirement:** Sort grouped results
```sql
SELECT Account.Industry, SUM(Amount) TotalRevenue
FROM Opportunity
WHERE StageName = 'Closed Won'
GROUP BY Account.Industry
ORDER BY SUM(Amount) DESC
```

### Scenario 14: LIMIT in Child Query (Medium)
**Requirement:** Top 3 opportunities per account
```sql
SELECT Name,
       (SELECT Name, Amount FROM Opportunities 
        ORDER BY Amount DESC 
        LIMIT 3)
FROM Account
```

### Scenario 15: ORDER BY RecordType (Medium-Hard)
**Requirement:** Sort by RecordType name
```sql
SELECT Name, RecordType.Name FROM Opportunity 
ORDER BY RecordType.Name ASC, Name ASC
```

### Scenario 16: Dynamic ORDER BY in Apex (Hard)
**Requirement:** Runtime determined sort order
```apex
String sortField = 'Amount';
String sortOrder = 'DESC';
String query = 'SELECT Name, Amount FROM Opportunity ORDER BY ' + 
               sortField + ' ' + sortOrder + ' LIMIT 100';
List<Opportunity> opps = Database.query(query);
```

### Scenario 17: ORDER BY with DISTANCE Function (Hard)
**Requirement:** Sort by proximity to location
```sql
SELECT Name, BillingCity,
       DISTANCE(BillingAddress, GEOLOCATION(37.7749, -122.4194), 'mi') Distance
FROM Account
WHERE BillingCountry = 'USA'
ORDER BY DISTANCE(BillingAddress, GEOLOCATION(37.7749, -122.4194), 'mi') ASC
LIMIT 20
```

### Scenario 18: ORDER BY with TYPEOF (Ultra-Hard)
**Requirement:** Sort polymorphic results
```sql
SELECT Id, Subject,
       TYPEOF What
           WHEN Account THEN Name
           WHEN Opportunity THEN Amount
       END
FROM Task
ORDER BY Subject ASC
```

### Scenario 19: Complex Pagination with Multiple Sorts (Hard)
**Requirement:** Advanced pagination scenario
```sql
-- Page 1
SELECT Name, Amount, CloseDate FROM Opportunity 
WHERE StageName NOT IN ('Closed Won', 'Closed Lost')
ORDER BY CloseDate ASC, Amount DESC 
LIMIT 50

-- Page 2
SELECT Name, Amount, CloseDate FROM Opportunity 
WHERE StageName NOT IN ('Closed Won', 'Closed Lost')
ORDER BY CloseDate ASC, Amount DESC 
LIMIT 50 
OFFSET 50
```

### Scenario 20: ORDER BY with Complex Criteria (Ultra-Complex)
**Requirement:** Multi-level sorting with nulls and relationships
```sql
SELECT Name, 
       Amount, 
       CloseDate, 
       Account.Name, 
       Account.Owner.Name,
       Account.Industry
FROM Opportunity
WHERE StageName IN ('Proposal/Price Quote', 'Negotiation/Review')
AND CloseDate = NEXT_N_DAYS:30
ORDER BY Account.Industry ASC NULLS LAST,
         Account.Owner.Name ASC,
         Amount DESC NULLS LAST,
         CloseDate ASC
LIMIT 100
```

---

## LIKE OPERATOR

### Scenario 1: Starts With (Easy)
**Requirement:** Find accounts starting with "Tech"
```sql
SELECT Name FROM Account WHERE Name LIKE 'Tech%'
```

### Scenario 2: Ends With (Easy)
**Requirement:** Find emails ending with gmail.com
```sql
SELECT Email FROM Contact WHERE Email LIKE '%@gmail.com'
```

### Scenario 3: Contains (Easy)
**Requirement:** Find accounts containing "Global"
```sql
SELECT Name FROM Account WHERE Name LIKE '%Global%'
```

### Scenario 4: Single Character Wildcard (Medium)
**Requirement:** Find names like "Jon" or "Jan"
```sql
SELECT FirstName FROM Contact WHERE FirstName LIKE 'J_n'
```

### Scenario 5: Case Sensitivity (Medium)
**Requirement:** Search case-insensitive (LIKE is case-insensitive by default)
```sql
SELECT Name FROM Account WHERE Name LIKE '%acme%'
-- Matches: ACME, Acme, acme, AcMe
```

### Scenario 6: Multiple LIKE Conditions (Medium)
**Requirement:** Find accounts starting with A or ending with Inc
```sql
SELECT Name FROM Account 
WHERE Name LIKE 'A%' 
OR Name LIKE '%Inc'
```

### Scenario 7: LIKE with NOT (Medium)
**Requirement:** Find accounts not starting with "Test"
```sql
SELECT Name FROM Account WHERE Name NOT LIKE 'Test%'
```

### Scenario 8: Email Domain Search (Medium)
**Requirement:** Find all corporate email domains
```sql
SELECT Email FROM Contact 
WHERE Email LIKE '%@%.com'
AND Email NOT LIKE '%@gmail.com'
AND Email NOT LIKE '%@yahoo.com'
```

### Scenario 9: Phone Number Pattern (Medium)
**Requirement:** Find phone numbers with specific area code
```sql
SELECT Name, Phone FROM Contact WHERE Phone LIKE '415%'
```

### Scenario 10: Street Address Pattern (Medium-Hard)
**Requirement:** Find addresses on specific street
```sql
SELECT Name, BillingStreet FROM Account 
WHERE BillingStreet LIKE '%Main Street%'
```

### Scenario 11: Wildcard in Middle (Medium)
**Requirement:** Find products with version numbers
```sql
SELECT Name FROM Product2 WHERE Name LIKE 'Product_V%'
```

### Scenario 12: Multiple Wildcards (Medium)
**Requirement:** Complex pattern matching
```sql
SELECT Name FROM Account WHERE Name LIKE 'A%Corp%'
```

### Scenario 13: LIKE with Parent Field (Medium-Hard)
**Requirement:** Filter contacts by account name pattern
```sql
SELECT FirstName, LastName, Account.Name FROM Contact 
WHERE Account.Name LIKE '%Technology%'
```

### Scenario 14: Combined LIKE Patterns (Hard)
**Requirement:** Multiple pattern matching
```sql
SELECT Name FROM Lead 
WHERE (Company LIKE '%Inc%' OR Company LIKE '%LLC%' OR Company LIKE '%Corp%')
AND Email LIKE '%@%.com'
```

### Scenario 15: LIKE with Escape Characters (Hard)
**Requirement:** Search for literal % or _ characters
```sql
-- Find names with underscore
SELECT Name FROM Account WHERE Name LIKE '%\_%' ESCAPE '\'
```

### Scenario 16: International Characters (Medium)
**Requirement:** Search with special characters
```sql
SELECT Name FROM Contact WHERE Name LIKE 'José%'
```

### Scenario 17: URL Pattern Matching (Medium-Hard)
**Requirement:** Find records with specific URL patterns
```sql
SELECT Name, Website FROM Account 
WHERE Website LIKE 'https://www.%'
```

### Scenario 18: File Extension Pattern (Medium)
**Requirement:** Find attachments with specific extensions
```sql
SELECT Name FROM Attachment WHERE Name LIKE '%.pdf'
```

### Scenario 19: Complex Business Logic with LIKE (Hard)
**Requirement:** Find potential duplicate accounts
```sql
SELECT Name, Phone, Website FROM Account 
WHERE Name LIKE '%Inc%'
OR Name LIKE '%LLC%'
OR Name LIKE '%Corporation%'
OR Phone LIKE '%.%'  -- formatted phone numbers
ORDER BY Name
```

### Scenario 20: Advanced Pattern Recognition (Ultra-Complex)
**Requirement:** Identify accounts with specific naming conventions
```sql
SELECT Name, Industry, Type FROM Account 
WHERE (
    (Name LIKE 'A%' AND Industry = 'Technology')
    OR (Name LIKE 'B%' AND Industry = 'Finance')
    OR (Name LIKE '%Global%' AND Type = 'Customer')
)
AND (
    Website LIKE 'https://%'
    OR Website LIKE 'http://www.%'
)
AND Name NOT LIKE '%Test%'
AND Name NOT LIKE '%Demo%'
ORDER BY Name
LIMIT 100
```

---

## IN & NOT IN OPERATORS

### Scenario 1: Simple IN with Strings (Easy)
**Requirement:** Find opportunities in specific stages
```sql
SELECT Name FROM Opportunity 
WHERE StageName IN ('Prospecting', 'Qualification', 'Proposal/Price Quote')
```

### Scenario 2: IN with Numbers (Easy)
**Requirement:** Find accounts with specific employee counts
```sql
SELECT Name FROM Account 
WHERE NumberOfEmployees IN (100, 500, 1000)
```

### Scenario 3: NOT IN (Easy)
**Requirement:** Exclude closed opportunities
```sql
SELECT Name FROM Opportunity 
WHERE StageName NOT IN ('Closed Won', 'Closed Lost')
```

### Scenario 4: IN with IDs (Medium)
**Requirement:** Find specific records by ID
```sql
SELECT Name FROM Account 
WHERE Id IN ('001XX000003DHP0', '001XX000003DHP1', '001XX000003DHP2')
```

### Scenario 5: IN with Parent Field (Medium)
**Requirement:** Filter by account industry
```sql
SELECT Name, Account.Industry FROM Contact 
WHERE Account.Industry IN ('Technology', 'Finance', 'Healthcare')
```

### Scenario 6: NOT IN with Parent Field (Medium)
**Requirement:** Exclude specific account types
```sql
SELECT Name FROM Opportunity 
WHERE Account.Type NOT IN ('Competitor', 'Former Customer')
```

### Scenario 7: IN with Dates (Medium)
**Requirement:** Find records created on specific dates
```sql
SELECT Name FROM Lead 
WHERE CALENDAR_MONTH(CreatedDate) IN (1, 2, 3)  -- Q1 months
```

### Scenario 8: Semi-Join with IN (Medium-Hard)
**Requirement:** Find accounts that have opportunities
```sql
SELECT Name FROM Account 
WHERE Id IN (SELECT AccountId FROM Opportunity WHERE Amount > 50000)
```

### Scenario 9: Anti-Join with NOT IN (Medium-Hard)
**Requirement:** Find accounts without opportunities
```sql
SELECT Name FROM Account 
WHERE Id NOT IN (SELECT AccountId FROM Opportunity)
```

### Scenario 10: IN with Multiple Subqueries (Hard)
**Requirement:** Complex filtering with subqueries
```sql
SELECT Name FROM Contact 
WHERE AccountId IN (
    SELECT AccountId FROM Opportunity WHERE StageName = 'Closed Won'
)
AND Id NOT IN (
    SELECT WhoId FROM Task WHERE Status = 'Completed'
)
```

### Scenario 11: IN with RecordTypeId (Medium)
**Requirement:** Filter by multiple record types
```sql
SELECT Name FROM Opportunity 
WHERE RecordTypeId IN (
    SELECT Id FROM RecordType 
    WHERE SObjectType = 'Opportunity' 
    AND DeveloperName IN ('Enterprise', 'Strategic')
)
```

### Scenario 12: NOT IN for Exclusion Logic (Medium)
**Requirement:** Exclude test and demo data
```sql
SELECT Name FROM Account 
WHERE Name NOT IN ('Test Account', 'Demo Account', 'Sample Account')
```

### Scenario 13: IN with User IDs (Medium)
**Requirement:** Filter by specific owners
```sql
SELECT Name FROM Opportunity 
WHERE OwnerId IN (
    SELECT Id FROM User 
    WHERE Profile.Name = 'Sales Representative' 
    AND IsActive = true
)
```

### Scenario 14: Complex Semi-Join (Hard)
**Requirement:** Find contacts associated with high-value accounts
```sql
SELECT FirstName, LastName, Email FROM Contact 
WHERE AccountId IN (
    SELECT AccountId FROM Opportunity 
    WHERE Amount > 100000 
    AND StageName = 'Closed Won'
    AND CloseDate = THIS_YEAR
)
```

### Scenario 15: Multiple NOT IN Conditions (Hard)
**Requirement:** Exclude multiple criteria
```sql
SELECT Name FROM Lead 
WHERE Status NOT IN ('Converted', 'Disqualified')
AND LeadSource NOT IN ('Purchased List', 'Other')
AND Company NOT IN ('Test', 'Demo', 'Sample')
```

### Scenario 16: IN with Territory Assignment (Hard)
**Requirement:** Find accounts in specific territories
```sql
SELECT Name FROM Account 
WHERE Id IN (
    SELECT AccountId FROM ObjectTerritory2Association 
    WHERE Territory2Id IN (
        SELECT Id FROM Territory2 WHERE Name IN ('West', 'East')
    )
)
```

### Scenario 17: Dynamic IN List from Custom Setting (Hard)
**Requirement:** Use custom setting values in IN clause (Apex)
```apex
List<String> excludedStages = new List<String>();
for(CustomSetting__c cs : CustomSetting__c.getAll().values()) {
    excludedStages.add(cs.StageName__c);
}

String query = 'SELECT Name FROM Opportunity WHERE StageName NOT IN :excludedStages';
List<Opportunity> opps = Database.query(query);
```

### Scenario 18: Nested Subqueries with IN (Ultra-Hard)
**Requirement:** Multi-level filtering
```sql
SELECT Name FROM Opportunity 
WHERE AccountId IN (
    SELECT AccountId FROM Contact 
    WHERE Id IN (
        SELECT WhoId FROM Task 
        WHERE Status = 'Completed' 
        AND ActivityDate = LAST_N_DAYS:30
    )
)
AND StageName NOT IN ('Closed Won', 'Closed Lost')
```

### Scenario 19: IN with Campaign Members (Hard)
**Requirement:** Find leads in specific campaigns
```sql
SELECT Name, Company FROM Lead 
WHERE Id IN (
    SELECT LeadId FROM CampaignMember 
    WHERE CampaignId IN (
        SELECT Id FROM Campaign 
        WHERE Type = 'Webinar' 
        AND Status = 'Completed'
    )
    AND Status = 'Responded'
)
```

### Scenario 20: Complex Business Logic with IN/NOT IN (Ultra-Complex)
**Requirement:** Sophisticated filtering scenario
```sql
SELECT Name, Amount, StageName, Account.Name 
FROM Opportunity 
WHERE AccountId IN (
    -- Accounts with high revenue
    SELECT Id FROM Account WHERE AnnualRevenue > 1000000
)
AND Id IN (
    -- Opportunities with recent activity
    SELECT WhatId FROM Task 
    WHERE WhatId != null 
    AND ActivityDate = LAST_N_DAYS:30
)
AND OwnerId IN (
    -- Active sales reps in specific roles
    SELECT Id FROM User 
    WHERE IsActive = true 
    AND UserRole.Name IN ('Sales VP', 'Sales Manager', 'Senior Sales Rep')
)
AND StageName NOT IN ('Closed Won', 'Closed Lost')
AND AccountId NOT IN (
    -- Exclude accounts with open cases
    SELECT AccountId FROM Case WHERE IsClosed = false
)
ORDER BY Amount DESC
LIMIT 50
```

---

## NULL HANDLING

### Scenario 1: Check for NULL (Easy)
**Requirement:** Find contacts without email
```sql
SELECT Name FROM Contact WHERE Email = null
```

### Scenario 2: Check for NOT NULL (Easy)
**Requirement:** Find contacts with email
```sql
SELECT Name, Email FROM Contact WHERE Email != null
```

### Scenario 3: NULL in Number Field (Easy)
**Requirement:** Find accounts without annual revenue
```sql
SELECT Name FROM Account WHERE AnnualRevenue = null
```

### Scenario 4: Multiple NULL Checks (Medium)
**Requirement:** Find incomplete contact records
```sql
SELECT Name FROM Contact 
WHERE Email = null 
OR Phone = null 
OR MailingCity = null
```

### Scenario 5: NOT NULL with AND (Medium)
**Requirement:** Find complete contact records
```sql
SELECT Name, Email, Phone FROM Contact 
WHERE Email != null 
AND Phone != null 
AND MailingCity != null
```

### Scenario 6: NULL in Parent Field (Medium)
**Requirement:** Find contacts without account
```sql
SELECT FirstName, LastName FROM Contact WHERE AccountId = null
```

### Scenario 7: NULL vs Empty String (Medium)
**Requirement:** Understand NULL vs '' difference
```sql
-- NULL check
SELECT Name FROM Account WHERE Description = null

-- Empty string check  
SELECT Name FROM Account WHERE Description = ''

-- Either NULL or empty
SELECT Name FROM Account WHERE (Description = null OR Description = '')
```

### Scenario 8: NULL in Date Fields (Medium)
**Requirement:** Find opportunities without close date
```sql
SELECT Name FROM Opportunity WHERE CloseDate = null
```

### Scenario 9: NULL in Lookup Relationships (Medium-Hard)
**Requirement:** Find opportunities without primary contact
```sql
SELECT Name FROM Opportunity WHERE ContactId = null
```

### Scenario 10: NULLS FIRST in Ordering (Medium)
**Requirement:** Show records without revenue first
```sql
SELECT Name, AnnualRevenue FROM Account 
ORDER BY AnnualRevenue ASC NULLS FIRST
```

### Scenario 11: NULLS LAST in Ordering (Medium)
**Requirement:** Show records with revenue first
```sql
SELECT Name, AnnualRevenue FROM Account 
ORDER BY AnnualRevenue DESC NULLS LAST
```

### Scenario 12: NULL in Formula Fields (Medium-Hard)
**Requirement:** Check if formula field returns null
```sql
SELECT Name, CustomFormula__c FROM Account 
WHERE CustomFormula__c = null
```

### Scenario 13: NULL Handling in Aggregates (Hard)
**Requirement:** COUNT ignores NULL values
```sql
-- Count all records
SELECT COUNT() FROM Contact

-- Count only records with email (excludes nulls)
SELECT COUNT(Email) FROM Contact
```

### Scenario 14: NULL in Multi-Select Picklist (Medium)
**Requirement:** Find records with no selections
```sql
SELECT Name FROM Account WHERE Products__c = null
```

### Scenario 15: Complex NULL Logic (Hard)
**Requirement:** Find accounts needing data enrichment
```sql
SELECT Name, Industry, AnnualRevenue, Phone, Website
FROM Account
WHERE (Industry = null OR AnnualRevenue = null)
AND (Phone = null OR Website = null)
AND Type = 'Customer'
ORDER BY CreatedDate DESC
```

### Scenario 16: NULL in Child Relationship (Hard)
**Requirement:** Accounts without any contacts
```sql
SELECT Name FROM Account 
WHERE Id NOT IN (SELECT AccountId FROM Contact WHERE AccountId != null)
```

### Scenario 17: NULL Coalescing in Apex (Hard)
**Requirement:** Provide default values for NULL fields
```apex
// In Apex
List<Account> accounts = [SELECT Name, AnnualRevenue FROM Account];
for(Account acc : accounts) {
    Decimal revenue = acc.AnnualRevenue != null ? acc.AnnualRevenue : 0;
    System.debug('Revenue: ' + revenue);
}
```

### Scenario 18: NULL in Geolocation (Hard)
**Requirement:** Find accounts without geolocation data
```sql
SELECT Name, BillingCity FROM Account 
WHERE BillingLatitude = null 
OR BillingLongitude = null
```

### Scenario 19: Complex NULL Validation (Ultra-Hard)
**Requirement:** Data quality check across multiple fields
```sql
SELECT Name, 
       Industry, 
       AnnualRevenue, 
       Phone, 
       Website,
       BillingStreet,
       BillingCity,
       BillingState
FROM Account
WHERE Type = 'Customer'
AND (
    Industry = null
    OR AnnualRevenue = null
    OR Phone = null
    OR Website = null
    OR BillingStreet = null
    OR BillingCity = null
    OR BillingState = null
)
ORDER BY CreatedDate DESC
LIMIT 100
```

### Scenario 20: NULL Handling with Complex Business Logic (Ultra-Complex)
**Requirement:** Comprehensive data completeness scoring
```sql
-- Query 1: Find incomplete high-value accounts
SELECT Name,
       AnnualRevenue,
       CASE WHEN Industry = null THEN 1 ELSE 0 END +
       CASE WHEN Phone = null THEN 1 ELSE 0 END +
       CASE WHEN Website = null THEN 1 ELSE 0 END +
       CASE WHEN BillingStreet = null THEN 1 ELSE 0 END MissingFields
FROM Account
WHERE AnnualRevenue > 1000000
AND (Industry = null OR Phone = null OR Website = null OR BillingStreet = null)
ORDER BY AnnualRevenue DESC

-- Note: CASE expressions shown for conceptual purposes
-- Actual implementation may require Apex for complex scoring
```

---

## GROUP BY & HAVING

### Scenario 1: Simple GROUP BY (Easy)
**Requirement:** Count opportunities by stage
```sql
SELECT StageName, COUNT(Id) 
FROM Opportunity 
GROUP BY StageName
```

### Scenario 2: GROUP BY with SUM (Easy)
**Requirement:** Total revenue by stage
```sql
SELECT StageName, SUM(Amount) 
FROM Opportunity 
GROUP BY StageName
```

### Scenario 3: GROUP BY with AVG (Medium)
**Requirement:** Average deal size by stage
```sql
SELECT StageName, AVG(Amount) AvgAmount 
FROM Opportunity 
GROUP BY StageName
```

### Scenario 4: GROUP BY Multiple Fields (Medium)
**Requirement:** Analyze by stage and owner
```sql
SELECT StageName, Owner.Name, COUNT(Id), SUM(Amount)
FROM Opportunity
GROUP BY StageName, Owner.Name
```

### Scenario 5: Simple HAVING (Medium)
**Requirement:** Find stages with more than 10 opportunities
```sql
SELECT StageName, COUNT(Id) 
FROM Opportunity 
GROUP BY StageName 
HAVING COUNT(Id) > 10
```

### Scenario 6: HAVING with SUM (Medium)
**Requirement:** Find stages with total revenue over $1M
```sql
SELECT StageName, SUM(Amount) TotalRevenue
FROM Opportunity
GROUP BY StageName
HAVING SUM(Amount) > 1000000
```

### Scenario 7: GROUP BY Parent Field (Medium)
**Requirement:** Revenue by account industry
```sql
SELECT Account.Industry, SUM(Amount) TotalRevenue
FROM Opportunity
WHERE StageName = 'Closed Won'
GROUP BY Account.Industry
```

### Scenario 8: GROUP BY with Date Function (Medium-Hard)
**Requirement:** Monthly revenue analysis
```sql
SELECT CALENDAR_MONTH(CloseDate) Month, SUM(Amount) Revenue
FROM Opportunity
WHERE CloseDate = THIS_YEAR
AND StageName = 'Closed Won'
GROUP BY CALENDAR_MONTH(CloseDate)
ORDER BY CALENDAR_MONTH(CloseDate)
```

### Scenario 9: GROUP BY with HAVING and ORDER BY (Hard)
**Requirement:** Top performing sales reps
```sql
SELECT Owner.Name, COUNT(Id) Deals, SUM(Amount) Revenue
FROM Opportunity
WHERE StageName = 'Closed Won'
AND CloseDate = THIS_YEAR
GROUP BY Owner.Name
HAVING SUM(Amount) > 500000
ORDER BY SUM(Amount) DESC
```

### Scenario 10: GROUP BY RecordType (Medium-Hard)
**Requirement:** Analyze opportunities by record type
```sql
SELECT RecordType.Name, COUNT(Id), AVG(Amount)
FROM Opportunity
GROUP BY RecordType.Name
```

### Scenario 11: Complex HAVING with Multiple Conditions (Hard)
**Requirement:** Find high-performing accounts
```sql
SELECT Account.Name, COUNT(Id) OpportunityCount, SUM(Amount) TotalRevenue
FROM Opportunity
WHERE StageName = 'Closed Won'
GROUP BY Account.Name
HAVING COUNT(Id) > 5
AND SUM(Amount) > 1000000
ORDER BY SUM(Amount) DESC
```

### Scenario 12: GROUP BY with CALENDAR_QUARTER (Medium-Hard)
**Requirement:** Quarterly revenue analysis
```sql
SELECT CALENDAR_QUARTER(CloseDate) Quarter, 
       CALENDAR_YEAR(CloseDate) Year,
       SUM(Amount) Revenue
FROM Opportunity
WHERE StageName = 'Closed Won'
GROUP BY CALENDAR_QUARTER(CloseDate), CALENDAR_YEAR(CloseDate)
ORDER BY CALENDAR_YEAR(CloseDate), CALENDAR_QUARTER(CloseDate)
```

### Scenario 13: GROUP BY Multiple Dimensions (Hard)
**Requirement:** Multi-dimensional analysis
```sql
SELECT Account.Industry, 
       Owner.Name, 
       StageName,
       COUNT(Id) OpportunityCount,
       SUM(Amount) TotalRevenue,
       AVG(Amount) AvgDealSize
FROM Opportunity
WHERE CloseDate = THIS_YEAR
GROUP BY Account.Industry, Owner.Name, StageName
ORDER BY SUM(Amount) DESC
```

### Scenario 14: GROUP BY with Currency (Hard)
**Requirement:** Analyze by currency type
```sql
SELECT CurrencyIsoCode, 
       COUNT(Id) Opportunities,
       SUM(Amount) TotalAmount,
       AVG(Amount) AvgAmount
FROM Opportunity
WHERE CloseDate = THIS_YEAR
GROUP BY CurrencyIsoCode
ORDER BY SUM(Amount) DESC
```

### Scenario 15: HAVING with AVG (Medium-Hard)
**Requirement:** Find industries with high average deal size
```sql
SELECT Account.Industry, AVG(Amount) AvgDealSize, COUNT(Id)
FROM Opportunity
WHERE StageName = 'Closed Won'
GROUP BY Account.Industry
HAVING AVG(Amount) > 75000
ORDER BY AVG(Amount) DESC
```

### Scenario 16: GROUP BY Lead Source Analysis (Medium)
**Requirement:** Conversion analysis by lead source
```sql
SELECT LeadSource, 
       COUNT(Id) TotalLeads,
       SUM(CASE WHEN IsConverted = true THEN 1 ELSE 0 END) ConvertedLeads
FROM Lead
GROUP BY LeadSource
ORDER BY COUNT(Id) DESC
```

### Scenario 17: GROUP BY with Time-Based Bucketing (Hard)
**Requirement:** Analyze opportunities by age
```sql
SELECT 
    CASE 
        WHEN CALENDAR_MONTH(CreatedDate) = CALENDAR_MONTH(TODAY) 
            THEN 'This Month'
        WHEN CALENDAR_MONTH(CreatedDate) = CALENDAR_MONTH(TODAY) - 1 
            THEN 'Last Month'
        ELSE 'Older'
    END AS AgeGroup,
    COUNT(Id),
    SUM(Amount)
FROM Opportunity
GROUP BY 
    CASE 
        WHEN CALENDAR_MONTH(CreatedDate) = CALENDAR_MONTH(TODAY) 
            THEN 'This Month'
        WHEN CALENDAR_MONTH(CreatedDate) = CALENDAR_MONTH(TODAY) - 1 
            THEN 'Last Month'
        ELSE 'Older'
    END
-- Note: CASE in GROUP BY requires workarounds in SOQL, typically done in Apex
```

### Scenario 18: HAVING with Multiple Aggregate Functions (Hard)
**Requirement:** Complex filtering on aggregates
```sql
SELECT Account.Name,
       COUNT(Id) OpportunityCount,
       SUM(Amount) TotalRevenue,
       AVG(Amount) AvgDealSize,
       MAX(Amount) LargestDeal
FROM Opportunity
WHERE StageName = 'Closed Won'
AND CloseDate = LAST_N_YEARS:2
GROUP BY Account.Name
HAVING COUNT(Id) > 3
AND SUM(Amount) > 500000
AND AVG(Amount) > 50000
ORDER BY SUM(Amount) DESC
LIMIT 50
```

### Scenario 19: GROUP BY Territory Analysis (Ultra-Hard)
**Requirement:** Performance by territory
```sql
SELECT Territory2.Name,
       COUNT(DISTINCT AccountId) AccountCount,
       SUM(Amount) TotalRevenue
FROM Opportunity
WHERE Id IN (
    SELECT OpportunityId FROM OpportunityTerritory2Association
)
AND StageName = 'Closed Won'
AND CloseDate = THIS_YEAR
GROUP BY Territory2.Name
HAVING SUM(Amount) > 1000000
ORDER BY SUM(Amount) DESC
-- Note: Territory queries require proper setup
```

### Scenario 20: Complex Multi-Dimensional Grouping (Ultra-Complex)
**Requirement:** Comprehensive sales analysis
```sql
SELECT 
    CALENDAR_YEAR(CloseDate) Year,
    CALENDAR_QUARTER(CloseDate) Quarter,
    Account.Industry,
    Owner.UserRole.Name Role,
    RecordType.Name RecordType,
    COUNT(Id) OpportunityCount,
    SUM(Amount) TotalRevenue,
    AVG(Amount) AvgDealSize,
    MIN(Amount) SmallestDeal,
    MAX(Amount) LargestDeal
FROM Opportunity
WHERE StageName = 'Closed Won'
AND CloseDate = LAST_N_YEARS:3
AND Account.Industry != null
GROUP BY 
    CALENDAR_YEAR(CloseDate),
    CALENDAR_QUARTER(CloseDate),
    Account.Industry,
    Owner.UserRole.Name,
    RecordType.Name
HAVING COUNT(Id) > 2
AND SUM(Amount) > 100000
ORDER BY 
    CALENDAR_YEAR(CloseDate) DESC,
    CALENDAR_QUARTER(CloseDate) DESC,
    SUM(Amount) DESC
LIMIT 200
```

---

## SEMI-JOINS & ANTI-JOINS

### Scenario 1: Basic Semi-Join (Easy)
**Requirement:** Find accounts that have opportunities
```sql
SELECT Name FROM Account 
WHERE Id IN (SELECT AccountId FROM Opportunity)
```

### Scenario 2: Basic Anti-Join (Easy)
**Requirement:** Find accounts without opportunities
```sql
SELECT Name FROM Account 
WHERE Id NOT IN (SELECT AccountId FROM Opportunity)
```

### Scenario 3: Semi-Join with Filter (Medium)
**Requirement:** Accounts with high-value opportunities
```sql
SELECT Name FROM Account 
WHERE Id IN (
    SELECT AccountId FROM Opportunity WHERE Amount > 100000
)
```

### Scenario 4: Anti-Join with Filter (Medium)
**Requirement:** Accounts without won opportunities
```sql
SELECT Name FROM Account 
WHERE Id NOT IN (
    SELECT AccountId FROM Opportunity WHERE StageName = 'Closed Won'
)
```

### Scenario 5: Semi-Join with Date Filter (Medium)
**Requirement:** Accounts with recent opportunities
```sql
SELECT Name FROM Account 
WHERE Id IN (
    SELECT AccountId FROM Opportunity WHERE CreatedDate = LAST_N_DAYS:30
)
```

### Scenario 6: Multiple Semi-Joins (Medium-Hard)
**Requirement:** Accounts with both opportunities and cases
```sql
SELECT Name FROM Account 
WHERE Id IN (SELECT AccountId FROM Opportunity)
AND Id IN (SELECT AccountId FROM Case)
```

### Scenario 7: Semi-Join with Aggregate Subquery (Hard)
**Requirement:** Accounts with more than 5 opportunities
```sql
SELECT Name FROM Account 
WHERE Id IN (
    SELECT AccountId FROM Opportunity 
    GROUP BY AccountId 
    HAVING COUNT(Id) > 5
)
```

### Scenario 8: Anti-Join for Orphaned Records (Medium)
**Requirement:** Find contacts without accounts (orphaned)
```sql
SELECT FirstName, LastName FROM Contact 
WHERE AccountId NOT IN (SELECT Id FROM Account)
OR AccountId = null
```

### Scenario 9: Semi-Join with Campaign Members (Medium-Hard)
**Requirement:** Leads who responded to campaigns
```sql
SELECT Name, Company FROM Lead 
WHERE Id IN (
    SELECT LeadId FROM CampaignMember 
    WHERE Status = 'Responded'
)
```

### Scenario 10: Anti-Join for Inactive Customers (Medium-Hard)
**Requirement:** Customers without recent activity
```sql
SELECT Name FROM Account 
WHERE Type = 'Customer'
AND Id NOT IN (
    SELECT AccountId FROM Opportunity 
    WHERE CreatedDate = LAST_N_MONTHS:6
)
AND Id NOT IN (
    SELECT AccountId FROM Case 
    WHERE CreatedDate = LAST_N_MONTHS:6
)
```

### Scenario 11: Semi-Join with Task Completion (Medium-Hard)
**Requirement:** Opportunities with completed tasks
```sql
SELECT Name FROM Opportunity 
WHERE Id IN (
    SELECT WhatId FROM Task 
    WHERE Status = 'Completed'
    AND WhatId != null
)
```

### Scenario 12: Anti-Join for Leads Never Contacted (Hard)
**Requirement:** Leads without any activities
```sql
SELECT Name, Company FROM Lead 
WHERE Id NOT IN (
    SELECT WhoId FROM Task WHERE WhoId != null
)
AND Id NOT IN (
    SELECT WhoId FROM Event WHERE WhoId != null
)
AND IsConverted = false
```

### Scenario 13: Semi-Join with Multiple Criteria (Hard)
**Requirement:** Active accounts with recent high-value deals
```sql
SELECT Name, Industry FROM Account 
WHERE Id IN (
    SELECT AccountId FROM Opportunity 
    WHERE Amount > 50000
    AND StageName = 'Closed Won'
    AND CloseDate = THIS_YEAR
)
AND Active__c = true
```

### Scenario 14: Complex Anti-Join (Hard)
**Requirement:** Opportunities without any attachments or notes
```sql
SELECT Name FROM Opportunity 
WHERE Id NOT IN (
    SELECT ParentId FROM Attachment WHERE ParentId != null
)
AND Id NOT IN (
    SELECT ParentId FROM Note WHERE ParentId != null
)
AND Id NOT IN (
    SELECT ParentId FROM ContentDocumentLink WHERE LinkedEntityId != null
)
```

### Scenario 15: Semi-Join with User Assignment (Medium-Hard)
**Requirement:** Opportunities owned by specific role users
```sql
SELECT Name FROM Opportunity 
WHERE OwnerId IN (
    SELECT Id FROM User 
    WHERE UserRole.Name = 'Sales VP'
    AND IsActive = true
)
```

### Scenario 16: Anti-Join for Territory Gaps (Hard)
**Requirement:** Accounts not assigned to any territory
```sql
SELECT Name, BillingState FROM Account 
WHERE Id NOT IN (
    SELECT AccountId FROM ObjectTerritory2Association
)
AND Type = 'Customer'
```

### Scenario 17: Semi-Join with Price Book (Medium-Hard)
**Requirement:** Products in specific price books
```sql
SELECT Name FROM Product2 
WHERE Id IN (
    SELECT Product2Id FROM PricebookEntry 
    WHERE Pricebook2Id IN (
        SELECT Id FROM Pricebook2 WHERE IsStandard = false
    )
)
```

### Scenario 18: Complex Multi-Level Semi-Join (Ultra-Hard)
**Requirement:** Contacts at accounts with opportunities owned by top performers
```sql
SELECT FirstName, LastName, Account.Name FROM Contact 
WHERE AccountId IN (
    SELECT AccountId FROM Opportunity 
    WHERE OwnerId IN (
        SELECT OwnerId FROM Opportunity 
        WHERE StageName = 'Closed Won'
        AND CloseDate = THIS_YEAR
        GROUP BY OwnerId 
        HAVING SUM(Amount) > 1000000
    )
    AND StageName NOT IN ('Closed Won', 'Closed Lost')
)
```

### Scenario 19: Anti-Join for Data Quality (Hard)
**Requirement:** Accounts without proper contact information
```sql
SELECT Name FROM Account 
WHERE Type = 'Customer'
AND Id NOT IN (
    SELECT AccountId FROM Contact 
    WHERE Email != null 
    AND Phone != null
    AND AccountId != null
)
```

### Scenario 20: Ultra-Complex Business Scenario (Ultra-Complex)
**Requirement:** Identify at-risk customers
```sql
-- Customers without recent won opportunities
SELECT Name, Industry, AnnualRevenue, Owner.Name
FROM Account 
WHERE Type = 'Customer'
-- Has had opportunities before
AND Id IN (
    SELECT AccountId FROM Opportunity 
    WHERE StageName = 'Closed Won'
    AND CloseDate < LAST_N_MONTHS:12
)
-- But no recent won opportunities
AND Id NOT IN (
    SELECT AccountId FROM Opportunity 
    WHERE StageName = 'Closed Won'
    AND CloseDate = LAST_N_MONTHS:6
)
-- And no recent activity
AND Id NOT IN (
    SELECT AccountId FROM Task 
    WHERE Status = 'Completed'
    AND ActivityDate = LAST_N_MONTHS:3
    AND AccountId != null
)
-- And has open support cases
AND Id IN (
    SELECT AccountId FROM Case 
    WHERE IsClosed = false
    AND Priority IN ('High', 'Critical')
)
ORDER BY AnnualRevenue DESC
LIMIT 100
```

---

## SOSL SEARCHES

### Scenario 1: Basic SOSL Search (Easy)
**Requirement:** Search for "Acme" across all objects
```sql
FIND {Acme} IN ALL FIELDS RETURNING Account, Contact, Lead
```

### Scenario 2: Search Specific Fields (Easy)
**Requirement:** Search in name fields only
```sql
FIND {John Smith} IN NAME FIELDS RETURNING Contact, Lead
```

### Scenario 3: Email Search (Easy)
**Requirement:** Find specific email
```sql
FIND {john@example.com} IN EMAIL FIELDS RETURNING Contact, Lead
```

### Scenario 4: Phone Search (Easy)
**Requirement:** Search phone numbers
```sql
FIND {415} IN PHONE FIELDS RETURNING Contact(Name, Phone), Lead(Name, Phone)
```

### Scenario 5: Wildcard Search (Medium)
**Requirement:** Search with wildcard
```sql
FIND {Acme*} IN ALL FIELDS RETURNING Account(Name, Industry)
```

### Scenario 6: Multiple Objects with Fields (Medium)
**Requirement:** Search and return specific fields
```sql
FIND {Technology} RETURNING 
    Account(Name, Industry, AnnualRevenue),
    Contact(FirstName, LastName, Email),
    Opportunity(Name, Amount, StageName)
```

### Scenario 7: Search with WHERE Clause (Medium)
**Requirement:** Filter SOSL results
```sql
FIND {Smith} IN ALL FIELDS RETURNING 
    Contact(FirstName, LastName, Email WHERE MailingState = 'CA')
```

### Scenario 8: SOSL with ORDER BY (Medium)
**Requirement:** Sort search results
```sql
FIND {Acme} RETURNING 
    Account(Name, AnnualRevenue ORDER BY AnnualRevenue DESC)
```

### Scenario 9: SOSL with LIMIT (Medium)
**Requirement:** Limit search results
```sql
FIND {John} RETURNING 
    Contact(FirstName, LastName LIMIT 5),
    Lead(FirstName, LastName LIMIT 5)
```

### Scenario 10: Phrase Search (Medium)
**Requirement:** Search exact phrase
```sql
FIND {"Global Technology Solutions"} IN ALL FIELDS RETURNING Account
```

### Scenario 11: Boolean Search - AND (Medium-Hard)
**Requirement:** Search with AND operator
```sql
FIND {John AND Smith} RETURNING Contact(Name, Email)
```

### Scenario 12: Boolean Search - OR (Medium-Hard)
**Requirement:** Search with OR operator
```sql
FIND {John OR Jane} RETURNING Contact(FirstName, LastName, Email)
```

### Scenario 13: Search with NOT (Medium-Hard)
**Requirement:** Exclude terms from search
```sql
FIND {Technology NOT Software} RETURNING Account(Name, Industry)
```

### Scenario 14: SOSL in Sidebar Fields (Medium)
**Requirement:** Search sidebar (commonly searched fields)
```sql
FIND {Acme} IN SIDEBAR FIELDS RETURNING Account, Contact, Opportunity
```

### Scenario 15: Complex Filter with Multiple Conditions (Hard)
**Requirement:** Advanced filtering of results
```sql
FIND {Technology} RETURNING 
    Account(Name, Industry, AnnualRevenue 
            WHERE Industry = 'Technology' 
            AND AnnualRevenue > 1000000
            ORDER BY AnnualRevenue DESC 
            LIMIT 10)
```

### Scenario 16: Search Custom Objects (Medium)
**Requirement:** Include custom objects in search
```sql
FIND {Project} RETURNING 
    Account(Name),
    CustomProject__c(Name, Status__c, Owner.Name)
```

### Scenario 17: Multi-Field Search Strategy (Hard)
**Requirement:** Search across different field types
```sql
FIND {*415* OR *john@* OR *Smith*} RETURNING 
    Contact(FirstName, LastName, Email, Phone 
            WHERE Email != null OR Phone != null)
```

### Scenario 18: SOSL with Relationship Fields (Hard)
**Requirement:** Return parent fields in results
```sql
FIND {Urgent} RETURNING 
    Case(CaseNumber, Subject, Status, Account.Name, Contact.Name 
         WHERE Priority = 'High' 
         ORDER BY CreatedDate DESC 
         LIMIT 20)
```

### Scenario 19: Division-Specific Search (Hard - if divisions enabled)
**Requirement:** Search within specific division
```sql
FIND {Acme} IN ALL FIELDS RETURNING 
    Account(Name, Division WHERE Division = 'North America')
```

### Scenario 20: Ultra-Complex Global Search (Ultra-Complex)
**Requirement:** Comprehensive customer search across all touchpoints
```sql
FIND {*Acme* OR *Global* OR *Technology*} IN ALL FIELDS RETURNING 
    Account(
        Name, 
        Industry, 
        AnnualRevenue, 
        Owner.Name,
        BillingState
        WHERE Type = 'Customer' 
        AND AnnualRevenue > 100000
        ORDER BY AnnualRevenue DESC 
        LIMIT 10
    ),
    Contact(
        FirstName, 
        LastName, 
        Email, 
        Phone, 
        Account.Name,
        Title
        WHERE Email != null 
        AND Account.Type = 'Customer'
        ORDER BY LastName 
        LIMIT 20
    ),
    Opportunity(
        Name, 
        Amount, 
        StageName, 
        CloseDate,
        Account.Name,
        Owner.Name
        WHERE StageName NOT IN ('Closed Won', 'Closed Lost')
        AND CloseDate = NEXT_N_MONTHS:3
        ORDER BY Amount DESC 
        LIMIT 15
    ),
    Case(
        CaseNumber, 
        Subject, 
        Status, 
        Priority,
        Account.Name,
        Contact.Name,
        Owner.Name
        WHERE IsClosed = false
        ORDER BY Priority DESC, CreatedDate DESC 
        LIMIT 10
    ),
    Lead(
        FirstName,
        LastName,
        Company,
        Email,
        Status,
        Owner.Name
        WHERE IsConverted = false
        AND Status IN ('Open', 'Working')
        ORDER BY CreatedDate DESC
        LIMIT 10
    )
```

---

## ADVANCED SCENARIOS - REAL WORLD USE CASES

### Use Case 1: Sales Pipeline Health Check (Ultra-Complex)
**Requirement:** Comprehensive pipeline analysis
```sql
-- Active opportunities approaching close date
SELECT Name,
       Amount,
       StageName,
       CloseDate,
       CALENDAR_MONTH(CloseDate) CloseMonth,
       Account.Name,
       Account.Industry,
       Owner.Name,
       Owner.Manager.Name,
       Age__c,
       (SELECT Subject, Status FROM Tasks WHERE Status != 'Completed') OpenTasks,
       (SELECT Subject FROM Events WHERE ActivityDate >= TODAY) UpcomingEvents
FROM Opportunity
WHERE IsClosed = false
AND CloseDate = NEXT_N_DAYS:60
AND Amount > 25000
AND StageName NOT IN ('Prospecting', 'Qualification')
AND OwnerId IN (
    SELECT Id FROM User 
    WHERE IsActive = true 
    AND Profile.Name IN ('Sales Representative', 'Account Executive')
)
ORDER BY CloseDate ASC, Amount DESC
LIMIT 100
```

### Use Case 2: Customer Churn Risk Analysis (Ultra-Complex)
**Requirement:** Identify at-risk customers
```sql
-- High-value customers with declining engagement
SELECT Name,
       AnnualRevenue,
       Industry,
       LastActivityDate,
       Owner.Name,
       (SELECT COUNT() FROM Opportunities 
        WHERE StageName = 'Closed Won' 
        AND CloseDate = LAST_N_MONTHS:12) RecentWins,
       (SELECT COUNT() FROM Cases 
        WHERE IsClosed = false 
        AND Priority = 'High') OpenHighPriorityCases
FROM Account
WHERE Type = 'Customer'
AND AnnualRevenue > 500000
AND LastActivityDate < LAST_N_DAYS:90
AND Id IN (
    SELECT AccountId FROM Opportunity 
    WHERE StageName = 'Closed Won'
    AND CloseDate < LAST_N_MONTHS:12
)
AND Id NOT IN (
    SELECT AccountId FROM Opportunity 
    WHERE CreatedDate = LAST_N_MONTHS:6
)
ORDER BY AnnualRevenue DESC
LIMIT 50
```

### Use Case 3: Marketing Campaign ROI (Ultra-Complex)
**Requirement:** Analyze campaign effectiveness with full funnel
```sql
-- Campaign performance with conversion metrics
SELECT Campaign.Name,
       Campaign.Type,
       Campaign.Status,
       Campaign.StartDate,
       COUNT(Id) TotalMembers,
       SUM(CASE WHEN Status = 'Responded' THEN 1 ELSE 0 END) Responses,
       SUM(CASE WHEN HasResponded = true AND LeadId != null THEN 1 ELSE 0 END) LeadResponses
FROM CampaignMember
WHERE Campaign.IsActive = true
AND Campaign.StartDate = THIS_YEAR
GROUP BY Campaign.Name, Campaign.Type, Campaign.Status, Campaign.StartDate
HAVING COUNT(Id) > 50
ORDER BY COUNT(Id) DESC
-- Note: Conversion to Opportunity requires additional queries or Apex
```

### Use Case 4: Territory Performance Dashboard (Ultra-Complex)
**Requirement:** Comprehensive territory analysis
```sql
-- Territory revenue and pipeline
SELECT Owner.UserRole.Name Territory,
       COUNT(Id) TotalOpportunities,
       SUM(CASE WHEN StageName = 'Closed Won' THEN Amount ELSE 0 END) WonRevenue,
       SUM(CASE WHEN IsClosed = false THEN Amount ELSE 0 END) Pipeline,
       AVG(CASE WHEN StageName = 'Closed Won' THEN Amount ELSE null END) AvgWonDeal,
       COUNT(DISTINCT AccountId) UniqueAccounts,
       COUNT(DISTINCT OwnerId) ActiveReps
FROM Opportunity
WHERE CloseDate = THIS_YEAR
AND Owner.IsActive = true
GROUP BY Owner.UserRole.Name
HAVING SUM(CASE WHEN StageName = 'Closed Won' THEN Amount ELSE 0 END) > 0
ORDER BY SUM(CASE WHEN StageName = 'Closed Won' THEN Amount ELSE 0 END) DESC
-- Note: CASE expressions shown conceptually; may need adjustment
```

### Use Case 5: Product Adoption Analysis (Ultra-Complex)
**Requirement:** Track product usage and upsell opportunities
```sql
-- Accounts with specific products, missing others
SELECT Account.Name,
       Account.Industry,
       Account.AnnualRevenue,
       COUNT(Id) CurrentProducts,
       (SELECT COUNT() FROM Opportunities 
        WHERE StageName = 'Closed Won' 
        AND CloseDate = THIS_YEAR) RecentPurchases
FROM OpportunityLineItem
WHERE Opportunity.StageName = 'Closed Won'
AND Product2.ProductCode IN ('PROD-A', 'PROD-B')
GROUP BY Account.Name, Account.Industry, Account.AnnualRevenue
HAVING COUNT(Id) < 5  -- Has some but not all products
ORDER BY Account.AnnualRevenue DESC
-- Additional query needed to identify missing products
```

---

