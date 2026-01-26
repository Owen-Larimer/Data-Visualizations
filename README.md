# Data-Visualizations
An unrelated list of visualizations and associated code


## Data Sources:
The data in this list will come from a multitude of sources, almost entirely loaded into jupyter using pd.read_csv().

### Global Fertility Rates

Here we'll look at the trend of global fertility rate and include a 95% confidence interval for it as well. The data here comes from a global_development set looking like:
<img width="2488" height="415" alt="image" src="https://github.com/user-attachments/assets/901d2663-527f-4e16-bef6-310e07fa5e50" />  

This figure will be made entirely with Matplotlib:


```python
#Group the data appropriately (will need the mean, std, and maybe count:
global_fertility = gd.groupby('Year')['Fertility Rate'].agg(['mean', 'std', 'count']).reset_index()

#Now let's create the confidence interval. We'll calculate it, then find the upper and lower bounds separately
global_fertility['CI'] = 1.96 * (global_fertility['std'] / np.sqrt(global_fertility['count']))
global_fertility['upper'] = global_fertility['mean'] + global_fertility['CI']
global_fertility['lower'] = global_fertility['mean'] - global_fertility['CI'] #Put it all into the dataframe to make addition easy.
    
# Now lets plot the trend line
plt.plot(global_fertility['Year'], global_fertility["mean"])
plt.title('Global Fertility Rate by Year')
plt.xlabel("Year")
plt.ylabel("Mean Global Fertility Rate")

#Now we can fill the area (lighter opacity) in between the trend line and the edge of the CI. 
plt.fill_between(global_fertility['Year'], global_fertility['lower'], global_fertility['upper'], alpha=0.2)
```

<img width="1173" height="914" alt="image" src="https://github.com/user-attachments/assets/9ed8230f-e69b-4120-83e5-7f1bf9ec7748" />  

###


