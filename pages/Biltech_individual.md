```sql individual
SELECT  
    city, 
    AVG(latitude) AS latitude, 
    AVG(longitude) AS longitude, 
    TRIM(data_type) AS filter_type,
    -- Conditional aggregation for installed_capacity (numeric)
    CASE 
        WHEN TRIM(data_type) IN ('Thermal Power Plant', 'Competitors') THEN SUM(
            CASE 
                WHEN TRY_CAST(installed_capacity AS FLOAT) IS NOT NULL THEN CAST(installed_capacity AS FLOAT)
                ELSE 0
            END
        )
        ELSE NULL
    END AS total_capacity_numeric,
    -- Conditional aggregation for installed_capacity (text)
    STRING_AGG(CASE 
        WHEN TRIM(data_type) IN ('Sand Mining', 'Consumption') THEN installed_capacity
        ELSE NULL
    END, ', ') AS place_detail,
    COUNT(*) AS count_of_plants 
FROM  
    "biltech_combined" 
WHERE  
    ('${inputs.data_type.value}' = 'All' OR TRIM(data_type) = '${inputs.data_type.value}') 
GROUP BY  
    city, 
    filter_type
```

<Dropdown name="data_type">  
    <DropdownOption value="Thermal Power Plant"/> 
    <DropdownOption value="Competitors"/> 
    <DropdownOption value="Sand Mining"/>
     <DropdownOption value="Consumption"/>
</Dropdown> 

<PointMap  
    data={individual}  
    lat="latitude"  
    long="longitude" 
    value="count_of_plants"
    pointName="city"  
    height={500} 
    tooltipType="hover" 
    tooltip={[
        {id: 'city', showColumnName: false, valueClass: 'text-xl font-semibold'},
        {id: 'count_of_plants', fieldClass: 'text-[grey]', valueClass: 'text-[green]', suffix: 'cubic meters'},
        {id: 'place_detail', fieldClass: 'text-[grey]', valueClass: 'text-[blue]', prefix: 'Individual Capacities: '},
        {id: 'total_capacity_numeric', fieldClass: 'text-[grey]', valueClass: 'text-[blue]', prefix: 'Individual Capacities: '}
    ]}
/>