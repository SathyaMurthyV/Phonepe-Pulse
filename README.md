# Phonepe-Pulse
PhonePe Pulse - Data 
The Indian digital payments story has been getting a lot of Updates in the recent years . From the largest cities to the smallest villages, the payments as being done throung UPI for smarter transaction . PhonePe started in 2016 and has been a strong beneficiary of digitisation of payments in India. The PhonePe Pulse Dataset API is a first of its kind open data initiative in the payments space. This allows users for easy to pay in every nook and corner .

This dataset as been used for finding how much people actually use / transfer / receive payments through UPI

Approach
The above dataset as been approched on the basis of data extraction and execution using 
1. Pandas
2. SQL
3. Steamlit

Business Use Cases:
     Many business use cases are available but the case study wich I Have selected are as follows
1.Decoding Transaction Dynamics on PhonePe
2.Device Dominance and User Engagement Analysis
3.Insurance Penetration and Growth Potential Analysis
4.Transaction Analysis for Market Expansion
5.User Engagement and Growth Strategy

From the above case study we can find which city / district have high transaction , low transacrtion , device of app , registered users , and so on 

Some Code Eg for the above :

import pandas as pd # to store / change data
import json # in format of json file
import os # to get / interact with file
path="/content/pulse/data/aggregated/transaction/country/india/state/" # root path for agg_transaction
Agg_state_list=os.listdir(path) # All state list
Agg_trans_data={'State':[], 'Year':[],'Quarter':[],'Transaction_type':[], 'Transaction_count':[], 'Transaction_amount':[]}

for state_name in Agg_state_list: # state list
    state_path=path+state_name+"/"
    Agg_yr=os.listdir(state_path)
    for year in Agg_yr: #year list
        year_path=state_path+year+"/"
        Agg_yr_list=os.listdir(year_path)
        for quarter in Agg_yr_list: # quarter list
            quarter_path=year_path+quarter
            Data=open(quarter_path,'r')
            D=json.load(Data)
            for z in D['data']['transactionData']: # data extraction
              Name=z['name']
              count=z['paymentInstruments'][0]['count']
              amount=z['paymentInstruments'][0]['amount']
              Agg_trans_data['Transaction_type'].append(Name) # appending data in dictionary
              Agg_trans_data['Transaction_count'].append(count)
              Agg_trans_data['Transaction_amount'].append(amount)
              Agg_trans_data['State'].append(state_name)
              Agg_trans_data['Year'].append(year)
              Agg_trans_data['Quarter'].append(int(quarter.strip('.json')))

Agg_Trans_df=pd.DataFrame(Agg_trans_data) # converting dictionary to dataframe

The above is used for finding types of transaction , like wise some codes are as follows 

mport os
import json
import pandas as pd
path="/content/pulse/data/map/insurance/hover/country/india/state/"
Map_ins_list=os.listdir(path)
Map_ins_data={'State': [],'Year': [],'Quarter': [],'District': [],'Insurance_Count': [],'Insurance_Amount': []}
for state_name in Map_ins_list:
    state_path = path + state_name + "/"
    Map_yr = os.listdir(state_path)
    for year in Map_yr:
        year_path = state_path + year + "/"
        Map_yr_list = os.listdir(year_path)
        for quarter in Map_yr_list:
            quarter_path = year_path + quarter
            Data = open(quarter_path, 'r')
            D = json.load(Data)
            for z in D['data']['hoverDataList']:
                district = z['name']
                count = z['metric'][0]['count']
                amount = z['metric'][0]['amount']
                Map_ins_data['District'].append(district)
                Map_ins_data['Insurance_Count'].append(count)
                Map_ins_data['Insurance_Amount'].append(amount)
                Map_ins_data['State'].append(state_name)
                Map_ins_data['Year'].append(year)
                Map_ins_data['Quarter'].append(int(quarter.strip('.json')))
Map_ins_df = pd.DataFrame(Map_ins_data)

# Step 1: Query the latest two quarters' data
query = """
SELECT State, Year, Quarter, SUM(transaction_count) AS Total_Transactions
FROM aggregated_transactions
WHERE Year = 2023 AND (Quarter = '1' OR Quarter = '4')
GROUP BY State, Year, Quarter
ORDER BY State, Year, Quarter;
"""

latest_quarters = pd.read_sql_query(query, conn)
latest_quarters['Prev'] = latest_quarters.groupby('State')['Total_Transactions'].shift(1)
latest_quarters['Change'] = latest_quarters['Total_Transactions'] - latest_quarters['Prev']
declining_states = latest_quarters[latest_quarters['Change'] < 0]
declining_states[['State', 'Year', 'Quarter', 'Total_Transactions', 'Prev', 'Change']]


