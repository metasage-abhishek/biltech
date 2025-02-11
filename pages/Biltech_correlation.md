```sql thermal_vs_competitors
SELECT 
    city,
    MAX(CASE WHEN TRIM(data_type) = 'Thermal Power Plant' THEN installed_capacity END) AS thermal_installed_capacity,
    MAX(CASE WHEN TRIM(data_type) = 'Competitors' THEN installed_capacity END) AS competitors_installed_capacity
FROM 
    "biltech_combined"
WHERE 
    TRIM(data_type) IN ('Thermal Power Plant', 'Competitors')
GROUP BY 
    city
HAVING 
    MAX(CASE WHEN TRIM(data_type) = 'Thermal Power Plant' THEN installed_capacity END) IS NOT NULL
    AND MAX(CASE WHEN TRIM(data_type) = 'Competitors' THEN installed_capacity END) IS NOT NULL;
```

<ScatterPlot
data={thermal_vs_competitors}
x="thermal_installed_capacity"
y="competitors_installed_capacity"
pointLabel="city"
height={400}
xAxisTitle="Thermal Power Plant Installed Capacity (MW)"
yAxisTitle="Competitors Installed Capacity (MW)"
colorPalette={['#FF5733']}
series=city
tooltip={[
    {id: 'city', showColumnName: false, valueClass: 'text-xl font-semibold'}, // City name
    {id: 'thermal_installed_capacity', fieldClass: 'text-[grey]', valueClass: 'text-[blue]', suffix: ' MW'}, // Thermal Power Plant capacity
    {id: 'competitors_installed_capacity', fieldClass: 'text-[grey]', valueClass: 'text-[green]', suffix: ' MW'} // Competitors capacity
]}
/>