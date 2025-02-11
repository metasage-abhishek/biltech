```sql biltech_combined
SELECT  
    city, 
    AVG(latitude) AS latitude, 
    AVG(longitude) AS longitude, 
    TRIM(data_type) AS filter_type,
    SUM(
        CASE 
            WHEN data_type IN ('Thermal Power Plant', 'Competitors') THEN installed_capacity::numeric
            ELSE NULL
        END
    ) AS total_capacity, 
    COUNT(*) AS count_of_plants,
    STRING_AGG(
        CASE 
            WHEN data_type IN ('Consumption', 'Sand Mining') THEN installed_capacity::text
            ELSE NULL
        END, 
        ', '
    ) AS place_detail
FROM  
    "biltech_combined" 
WHERE  
    position('All' in ${inputs.filter.value}::text) > 0
    OR position(data_type in ${inputs.filter.value}::text) > 0
GROUP BY  
    city, 
    filter_type
order by city;
```



<Dropdown name="filter" multiple=true> 
    <DropdownOption value="All" valueLabel="All"/> 
    <DropdownOption value="Thermal Power Plant"/> 
    <DropdownOption value="Competitors"/> 
    <DropdownOption value="Sand Mining"/> 
    <DropdownOption value="Consumption"/> 
</Dropdown> 


<PointMap  
    data={biltech_combined}  
    lat="latitude"  
    long="longitude" 
    value="filter_type"
    pointName="city"  
    height={500} 
    tooltipType="hover" 
    colorPalette={['#FF0000', '#0000FF', '#00FF00', '#FFAA00']}
    tooltip={[
        {id: 'city', showColumnName: false, valueClass: 'text-xl font-semibold'},
        {id: 'filter_type', fieldClass: 'text-[grey]', valueClass: 'text-[green]', suffix: 'cubic meters'},
        {id: 'place_detail', fieldClass: 'text-[grey]', valueClass: 'text-[blue]', prefix: 'Individual Capacities: '},
        {id: 'total_capacity', fieldClass: 'text-[grey]', valueClass: 'text-[blue]', prefix: 'Individual Capacities: '}
    ]}
/>