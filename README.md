# Data-Visualizations
A list of unrelated visualizations and associated code.


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

**Notes:** WIP


### W.E.B. Du Bois Challenge - 2024 Black Challenge 5:

The data here comes speicifcally from the GitHub page for the Du Bois Figure Challenge, and looks like so:
<img width="400" height="261" alt="image" src="https://github.com/user-attachments/assets/40a95175-01ef-48c9-808d-15eadc1c64e8" />  

The goal of this project is to recreate one of W.E.B Du Bois' famous figures as closely as possible:

```python
stacked_bar = (c5.set_index('Category').loc[['Yellow', 'Brown', 'Black']].T)

ax = stacked_bar.plot(kind='bar', stacked=True, color=['gold', '#5C4033', 'black'], legend=True)

# ax.legend(loc='center left');

handles, labels = ax.get_legend_handles_labels() #Found online.
ax.legend(handles[::-1], labels[::-1], loc='center left', frameon=False)

percentage_values = stacked_bar.iloc[0].values  # Should be [16, 40, 44].
label_colors = ['black', 'red', 'white']

# Now we add the labels onto the middle of the bars using the proper colors.
bottom = 0
for val, txt_color in zip(percentage_values, label_colors):
    ax.text(x= 0, y= bottom + val / 2, s=f"{val}%", ha='center', va='center', fontweight='bold', color=txt_color)
    bottom += val

plt.title("Race Amalgamation in Georgia");
fig.set_facecolor('#D2B48C')
ax.axis('off');

```
<img width="810" height="827" alt="image" src="https://github.com/user-attachments/assets/09eabcd8-43ae-468a-9290-d34b73c1c85e" />


**Notes:** Red and green challenges to follow soon.



### San Diego Water Supply Origins

This is the recreation of a more complicated figure with the hopes of encoding the data in a much better, more readable way.

This is what the figure originally looked like:
<img width="1157" height="870" alt="image" src="https://github.com/user-attachments/assets/3dbfc631-94eb-4f49-9419-f8f61a317946" />  

I used that image to create the entire dataset in Excel, then passed it into a pandas dataframe so that it looked like this:
<img width="1188" height="365" alt="image" src="https://github.com/user-attachments/assets/4a69deb9-ca4c-453f-a1cb-d76f67ed7274" />  

and I pivoted it like so: 
```python
pivot_df = fig2.pivot(index='Year',columns='Water_Origins', values='Acre_Feet (Thousands)').fillna(0)
pivot_df
```
To look like this: 
<img width="2473" height="397" alt="image" src="https://github.com/user-attachments/assets/9b541ca4-c1f7-49b8-bc23-1da448e511a5" />  

And now to create a better figure. I decided a stacked barplot would be best:  

```python
fig, ax = plt.subplots()

sns.set_palette('Dark2')
pivot_df.plot(kind="bar", stacked=True, ax=ax, edgecolor="black", linewidth=1)

ax.legend(title="Water Origin", bbox_to_anchor=(1.02, 1), loc="upper left", frameon=False)
plt.xticks(rotation=40);

for container in ax.containers:
    labels = [f"{int(v)} TAF" if v > 0 else "" for v in container.datavalues] #STACKED
    ax.bar_label(container, labels=labels, label_type='center', fontsize=8)

plt.title("San Diego's Water Supply Diversification Throughout the Decades");
plt.ylabel("Thousands of Acre-Feet of Water")
plt.grid(axis = 'y')

totals = pivot_df.sum(axis=1)

for i, year in enumerate(pivot_df.index):
    ax.text(i, totals.loc[year] + 10, f"{int(totals.loc[year])}", ha='center', va='bottom', fontsize=10, fontweight='bold')
```  
<img width="1826" height="940" alt="image" src="https://github.com/user-attachments/assets/a5fc3e15-d17e-41aa-8495-96c45969d6eb" /> 

**Notes:** WIP



### GDP Growth Percentage Over Time

This was an attempt to recode the data an original figure conveyed. The original figure was a 3d barplot, looking like:

<img width="1037" height="1127" alt="image" src="https://github.com/user-attachments/assets/3061843d-8aa2-4f6b-8182-f5322403f9b4" />  

3d barplots are ultimately not very good at their job. They are difficult to read due to their depth, requiring the viewer to tilt their head in order to glean informaiton. It's also very difficult to compare intra-group trends against each other since the angle at which we have to view the 3d chart prevents the two groups from lining up perfectly.

The data was read in like so (after being made from scratch in excel): 

```python
fig3 = pd.read_csv("./DSCI_410_HW3_Data_Fig_3.csv")
fig3.head()
```
To result in: 
<img width="681" height="385" alt="image" src="https://github.com/user-attachments/assets/dcb088fe-a0e9-4626-a51b-63961854c105" />  

I decided to get rid of the 3d aspect altogether, instead opting for a line chart that would clearly show both regions' GDP growth trends over time:


```python
fig, ax = plt.subplots()

plt.plot(fig3['Year'], fig3['GDP_Growth_EU (%)'], label='EU', linewidth = 5, color='darkblue')
plt.plot(fig3['Year'], fig3['GDP_Growth_US (%)'], label='US', linewidth = 5, color='darkred')

plt.scatter(fig3['Year'], fig3['GDP_Growth_EU (%)'], edgecolor='black', s=90, color='yellow', zorder = 3, alpha =0.8)
plt.scatter(fig3['Year'], fig3['GDP_Growth_US (%)'], edgecolor='black', s=90, color='white', zorder = 3, alpha=0.8)

plt.title("GDP Growth Percentage Over Time for US and EU")
plt.ylabel("Year")
plt.xlabel("Percent Change")
plt.grid(axis = 'y')
plt.legend()
ax.set_ylim(bottom=min(ax.get_ylim()[0], 0))

for x, y in zip(fig3['Year'], fig3['GDP_Growth_EU (%)']):
    ax.text(x, y + 0.15, f"{y}", ha='center', va='bottom', fontsize=10, color='grey', fontweight='bold')

for x, y in zip(fig3['Year'], fig3['GDP_Growth_US (%)']):
    ax.text(x, y + 0.15, f"{y}", ha='center', va='bottom', fontsize=10, color='grey', fontweight='bold')
```

<img width="1109" height="908" alt="image" src="https://github.com/user-attachments/assets/27b61165-564d-42b6-b104-af68dcc27d03" />  

Though less visually impressive, this new figure eliminates all drawbacks of the original 3d barplot, as well as highly maximizes the data to ink ratio.


### Confusion Matrix


















