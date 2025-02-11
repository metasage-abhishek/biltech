```sql biltech__combined
SELECT  
    city, 
    AVG(latitude) AS latitude, 
    AVG(longitude) AS longitude,  
    COUNT(*) AS count
FROM (
    SELECT  
        city, 
        data_type,
        latitude, 
        longitude
    FROM  
        "biltech_combined" 
    WHERE  
        POSITION('All' IN ${inputs.filter.value}::TEXT) > 0
        OR POSITION(data_type IN ${inputs.filter.value}::TEXT) > 0
) subquery
GROUP BY  
    city
ORDER BY  
    city;
```


<Dropdown name="filter" multiple=true> 
    <DropdownOption value="All" valueLabel="All"/> 
    <DropdownOption value="Thermal Power Plant" valueLabel="Thermal Power Plant"/> 
    <DropdownOption value="Competitors" valueLabel="Competitors"/> 
    <DropdownOption value="Sand Mining" valueLabel="Sand Mining"/> 
    <DropdownOption value="Consumption" valueLabel="Consumption"/> 
</Dropdown>


<BubbleMap  
    data={biltech__combined}  
    lat="latitude"  
    long="longitude"
    size=count 
    value="count"
    pointName=city 
    height={500} 
    tooltipType="hover" 
    colorPalette={['#FF0000', '#0000FF', '#00FF00', '#FFAA00']}
    tooltip={[
        {id: 'city', showColumnName: false, valueClass: 'text-xl font-semibold'},
        {id: 'count', fieldClass: 'text-[grey]', valueClass: 'text-[green]', suffix: 'cubic meters'},
    ]}
/>