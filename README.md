# 111
# Filter out unavailable ports
stations_to_remove = ['Hung Hom', 'Sha Tau Kok', 'Tuen Mun Ferry Terminal']
data = data[~data['Control Point'].isin(stations_to_remove)].copy()

# Processing abnormal values at ports affected by the COVID-19
zero_periods = {
    'Hong Kong-Macau Ferry Terminal': ('2021-01-01', '2023-01-07'),
    'Harbour Control': ('2021-01-01', '2023-04-03'),
    'Express Rail Link West Kowloon': ('2021-01-01', '2023-01-14'),
    'Lo Wu': ('2021-01-01', '2023-02-06'),
    'Lok Ma Chau': ('2021-01-01', '2023-02-05'),
    'Lok Ma Chau Spur Line': ('2021-01-01', '2023-01-07'),
    'China Ferry Terminal': ('2021-01-01', '2023-01-07'),
    'Heung Yuen Wai': ('2021-01-01', '2023-02-05'),
    'Man Kam To': [('2023-09-08', '2023-11-12'), ('2021-01-01', '2023-01-07')],
    'Kai Tak Cruise Terminal': [('2021-01-01', '2021-07-30'), ('2022-01-06', '2023-02-10')]
}  # station, (start, end)

for station, periods in zero_periods.items():
    if isinstance(periods, tuple):
        periods = [periods]
    for start, end in periods:
        mask = (
            (data['Control Point'] == station)
            & (data['Date'] >= pd.to_datetime(start))
            & (data['Date'] <= pd.to_datetime(end))
            & (
                (data['Hong Kong Residents'] == 0)
                | (data['Mainland Visitors'] == 0)
                | (data['Total'] == 0)
            )
        )
        data.loc[mask, ['Hong Kong Residents', 'Mainland Visitors', 'Total']] = np.nan

print(data.head())
