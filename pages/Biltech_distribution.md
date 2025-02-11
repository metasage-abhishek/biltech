```sql city_counts
SELECT  
    TRIM(data_type) AS filter_type,
    COUNT(DISTINCT city) AS city_count
FROM  
    "biltech_combined" 
WHERE  
    position('All' in ${inputs.filter.value}::text) > 0
    OR position(data_type in ${inputs.filter.value}::text) > 0
GROUP BY  
    filter_type
ORDER BY  
    city_count DESC;
```

<Dropdown name="filter" multiple=true> 
    <DropdownOption value="All" valueLabel="All"/> 
    <DropdownOption value="Thermal Power Plant"/> 
    <DropdownOption value="Competitors"/> 
    <DropdownOption value="Sand Mining"/> 
    <DropdownOption value="Consumption"/> 
</Dropdown> 

<BarChart
data={city_counts}
category="filter_type"
value="city_count"
height={300}
colorPalette={['#FF0000', '#0000FF', '#00FF00', '#FFAA00']}
tooltip={[
{id: 'filter_type', showColumnName: false, valueClass: 'text-xl font-semibold'},
{id: 'city_count', fieldClass: 'text-[grey]', valueClass: 'text-[blue]', suffix: ' cities'}
]}
/>

```sql capacity_counts
SELECT  
    CASE 
        WHEN POSITION('airport' IN LOWER(installed_capacity)) > 0 THEN 'Airport'
        ELSE installed_capacity
    END AS capacity_category,
    COUNT(*) AS capacity_count
FROM  
    "biltech_combined"
WHERE  
    TRIM(data_type) = 'Consumption'
    AND (position('All' in ${inputs.filter.value}::text) > 0
    OR position('Consumption' in ${inputs.filter.value}::text) > 0)
GROUP BY  
    CASE 
        WHEN POSITION('airport' IN LOWER(installed_capacity)) > 0 THEN 'Airport'
        ELSE installed_capacity
    END
ORDER BY  
    capacity_count DESC;
```

<BarChart
data={capacity_counts}
category="capacity_category"
value="capacity_count"
height={300}
colorPalette={['#FF5733', '#33FF57', '#3357FF', '#F3FF33']}
tooltip={[
{id: 'capacity_category', showColumnName: false, valueClass: 'text-xl font-semibold'},
{id: 'capacity_count', fieldClass: 'text-[grey]', valueClass: 'text-[blue]', suffix: ' occurrences'}
]}
/>