Investigate an electricity consumption dataset provided by University California Irvine

#### NOTEBOOKS:

1 - Data exploration\
2 - Prediction of the global active power (hourly averaged) using different algorithms\
3 - 15-min chunks preparation\
4 - Timeseries clustering\
5 - Knowledge graphs\

#### DESCRIPTION OF THE INPUT DATA:

1.date: Date in format dd/mm/yyyy  
2.time: time in format hh:mm:ss  
3.global_active_power: household global minute-averaged active power (in kilowatt)  
4.global_reactive_power: household global minute-averaged reactive power (in kilowatt)  
5.voltage: minute-averaged voltage (in volt)  
6.global_intensity: household global minute-averaged current intensity (in ampere)  
7.sub_metering_1: energy sub-metering No. 1 (in watt-hour of active energy). It corresponds to the kitchen, containing mainly a dishwasher, an oven and a microwave (hot plates are not electric but gas powered).  
8.sub_metering_2: energy sub-metering No. 2 (in watt-hour of active energy). It corresponds to the laundry room, containing a washing-machine, a tumble-drier, a refrigerator and a light.  
9.sub_metering_3: energy sub-metering No. 3 (in watt-hour of active energy). It corresponds to an electric water-heater and an air-conditioner.  

#### DESCRIPTION OF THE NOTEBOOKS:

1. Data exploration:
     * Replace NaNs (1.3%) with interpolated values
     * From the original minute-wise data, create hourly/daily/monthly average dataframes
     * Explore trends and data distributions (not normally distributed in most cases)
     * Split train (dec 2006 - dec 2009) and test data (2010)
     * Create lag features and keep only the relavant ones (using partial autocorrelation)
     * Rescale and backup data  
&nbsp;
  
2. Prediction of the global active power (hourly averaged):
   
   We want to predict the global active power (hourly averaged). We assume that we do not know the other instantaneous quantities (intensty, voltage, etc) but that we know their past (=lag) values. We also assume we do not have access to the sub-meters values. We use various algorithms:
     * scikit-learn linear regression
     * scikit-learn random forest (including assessment of feature importance)
     * lightgbm gradient boosting
     * xgboost gradient boosting\
     &nbsp;

     In each case, we provide evaluation metrics and visualization of the prediction.\
     &nbsp;
     ***All algorithms have similar performances: they forecast decently the time evolution, but they fail to capture the peak values, and the peaks predicted are slightly lagged when compared with the test values. Both are due to the fact that we use only lag values as input for the predictions, while external data (for instance air temperature, holidays, etc) would be required for a better performance.***

3. 15-mins chunks preparation:
     * From the original minute-wise data, we create 15-min average chunks, including those starting at t0 + 5min and t0 + 10 min.
     * We also create a large number of ad-hoc features, for instance chunk-average value, chunk standard deviation, etc for each of the quantities provided (active power, reactive power, intensity, voltage, etc).\
&nbsp;  

4. Timeseries clustering:

     In this notebook we perform tmeseries clustering to group individual active power chunks by their evolution over their 15-min duration.
     * We first select the appropriate number of clusters by investigating the global silhouette score for 5000 randomly selected chunks.
     * Then we attribute each randomly selected chunk to one of the clusters. The notebooks provide the visualization of individual timeseries clusters and their individual silhouette scores.\
&nbsp;  

5. Knowledge graphs:

     Using the model developed in notebook 5, we predict the classification of the entire sample of chunks. We then investigate the potential use of knowledge graphs to help predicting the next chunk.
     * We first prepare a directed knowledge graph and investigate a few of its properties.
     * There are too many individual connections, and the graph presents limited added value.
     * However, if we focus only on connections above a certain probability threshold (here 3%), we see that some nodes are very often incoming nodes, while others are only outgoing nodes. This unlocks the potential of knowledge graphs as a means to help us predicting the evolution of the global active power demand within the next quarter. 