# SMA-Modbus-energy-management-system
EMS in Home Assisant and Node-RED

I’ve created an Energy Management System (EMS) using Node-RED, running as an add-on in Home Assistant.

Goal:
The goal of this EMS is to make my classic SMA inverter smarter. I want to take advantage of favorable electricity prices and avoid negative situations using hourly dynamic pricing.

I started this in 2022, when I installed my solar panels:

12 panels (405W) facing South-East

10 panels (405W) facing South-West

Inverter: SMA Tripower 8 SE

Technical information of all modbus registers the inverter has, can be found here: 
https://files.sma.de/downloads/PARAMETER-HTML_STPxx-3SE-40_30109R_V11.zip

Battery: BYD

Energy meter: SMA Home Manager 2

⚠️ Important Notice:
I removed the SMA Home Manager 2 from Sunny Portal and only use Home Assistant to control my system. One caveat is that you lose all the data you collected there. 
Since the latest firmware update, Modbus register 41255 (used for curtailment control) gets overwritten by the Home Manager 2.
SMA is preparing their own EMS (expected Q3 2025), so they changed how the Home Manager interacts with the inverter.

To regain full control over Modbus, I unlinked the Home Manager 2 from Sunny Portal.
It now acts like a standard energy meter. It still controls the battery’s basic behavior, but I have full access to all modbus registers again.
If you use a classic SMA Energy Meter (not Home Manager 2), this limitation does not apply. 

My system is a work in progress and I am always trying to improve it. This means that adjustments will still occur. I started this back in 2022 - ... 

You are free to use the code in this project as inspiration, but you remain responsible yourself for what you do with it and for your own system. I am in no means liable for mistakes or damage to your own system. 

The flows in the screenshots contain some nodes that are not standard in Node Red. You will need to install them the following way. 

Node red --> menu on the right (hamburgermenu) --> manage palette --> klik install --> search modules : 
    - node-red-contrib-simple-gate
    - node-red-contrib-time-range-switch
    - node-red-contrib-traffic
    - node-red-contrib-controltimer
    - node-red-contrib-stoptimer

gate, within-time-switch, traffic, controletimer and install them one by one. 

I also made use of the EPEX SPOT integration and in addition the EPEX SPOT SENSOR integration that you can download true HACS community integrations.
You can make the for the most expensive price in the morning and evening binary sensors to use in the automations. 

Features in this Release 1.2:
    
    1. Zero export when export price is negative → Only generate energy used in the home.

    2. Negative import prices → Charge the battery during the 3 most negative price hours and stop PV production during that time.
       (still in test phase)

    3. Smart selling → Sell excess energy at peak price in the morning/evening, if solar forecast is strong (March–September).
       In the evening it will sell all energy above 70% SOC if the solar forecast the next day is high enough. 
       In the morning it will sell all energy above 20% SOC if the solar forecast the same day is high enough. 

    4. Delayed battery charging → Wait until export prices are lowest. 
       Always keeps 20% into the battery and charges up to 20% before delaying the rest of the charging. 
       It wil start charging if export price is negative sooner. If the battery is full it will automaticaly reduce the power it generates.
       It will only produce power that the house is using at that time.
       If the price turns positive again, then the PV panels will produce in full force again. 

    5. Winterflow → Charge battery during the 4 cheapest hours at night if solar forecast <15 kWh and price spread is significant.

    6. LowPV-mode → In winter PV production is at its lowest. 
       Use battery only during hours when import prices are above a defined threshold.

🛠️ Note:
Feel free to use or modify my flows for your own setup. You do so at your own risk.

<img width="1079" alt="image" src="https://github.com/user-attachments/assets/2870d665-b68d-4c2f-98ec-305f2fcc9b54" />

<img width="993" alt="image" src="https://github.com/user-attachments/assets/8b72d425-1f3d-4b6b-9fc8-65d8495aefa0" />

The file "NODE-RED FLOWS Vx.x . JSON" contains al the flows in the pictures below. You can import them in Node-RED. 

<img width="710" alt="image" src="https://github.com/user-attachments/assets/7757c1ec-6be6-4901-a245-476732700c47" />

<img width="865" alt="image" src="https://github.com/user-attachments/assets/931f85bc-b837-4b6b-83fc-0bab088401e3" />

<img width="862" alt="image" src="https://github.com/user-attachments/assets/e11d545a-55c6-4bb0-8dc2-4baec3e5149f" />

<img width="1497" alt="image" src="https://github.com/user-attachments/assets/b90ae947-f2d5-45d4-887b-432f3b519baa" />

<img width="1483" alt="image" src="https://github.com/user-attachments/assets/669d6f3c-d366-454e-90d9-9418013f31e3" />

<img width="1526" alt="image" src="https://github.com/user-attachments/assets/7e187848-4cfc-46bc-83fe-bed33e142076" />

The file SENSORS.YAML contains all of the the sensors I had to create in addition to already existing sensors. You will need to create them in your configuration.yaml file of your Home Assistant.  

The file SOLAR_FORECAST(APEXCHART) contains the code to use in an apexchart to make it visual of what is happening during the day. 

Example of what a day (11/06/2025) looks like:
<img width="377" alt="image" src="https://github.com/user-attachments/assets/4d111b06-32cf-4c58-90d6-b6613a883d32" />


I added a .JSON with a simplified flow for people with only an inverter, but without a battery, that want to limit the export to the grid and let the inverter only produce what the house is consuming. 
The flow looks like this:

<img width="1396" height="368" alt="image" src="https://github.com/user-attachments/assets/3a26ee2e-8b08-4011-a53e-eb316df5617f" />






