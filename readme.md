<div class="cell markdown">

# SNA Group Assignment

</div>

<div class="cell markdown">

Importing Libraries

</div>

<div class="cell code" data-execution_count="1">

``` python
import networkx as nx
import pandas as pd
import matplotlib.pyplot as plt
```

</div>

<div class="cell markdown">

Importing dataset

</div>

<div class="cell code" data-execution_count="2">

``` python
users = pd.read_csv('musae_git_target.csv')
```

</div>

<div class="cell markdown">

Reading Graphs

</div>

<div class="cell code" data-execution_count="3">

``` python
Data = open('musae_git_edges.csv', "r")

next(Data, None)  # skip the first line in the input file

followers = nx.parse_edgelist(Data, delimiter=',', create_using=nx.Graph(), nodetype=int)
```

</div>

<div class="cell markdown">

Finding Local Cluster (each nodes clustering coefficient)

</div>

<div class="cell code" data-execution_count="10">

``` python
local_cluster = nx.clustering(followers)
sorted_local_cluster = {k: v for k, v in sorted(local_cluster.items(), key=lambda item: item[1])}
sorted_local_cluster
```

<div class="output execute_result" data-execution_count="10">

    {0: 0,
     34526: 0,
     34035: 0,
     6067: 0,
     20183: 0,
     3: 0,
     4950: 0,
     4: 0,
     ...}

</div>

</div>

<div class="cell markdown">

Global Clustering with count zeroes (default)

</div>

<div class="cell code" data-execution_count="56">

``` python
global_cluster = nx.average_clustering(followers, count_zeros=True)
global_cluster
```

<div class="output execute_result" data-execution_count="56">

    0.16753704480107323

</div>

</div>

<div class="cell markdown">

eccentricity (shortest path with maximum number of nodes)

</div>

<div class="cell code" data-execution_count="69">

``` python
# import pickle
# with open('eccentricity.pkl', 'rb') as f:
#     eccentricity = pickle.load(f)

eccentricity = nx.eccentricity(followers)
sorted_eccentricity = {k: v for k, v in sorted(eccentricity.items(), key=lambda item: item[1])}

sorted_eccentricity = list(dict(sorted(eccentricity.items(), key=lambda item: item[1], reverse=True)).items())

# get index (node number) and value (node eccentricity value) top 10 after sorting
sorted_eccentricity_indexes = [x[0] for x in sorted_eccentricity]
sorted_eccentricity_values = [x[1] for x in sorted_eccentricity]

# Creating dataframe
top_sorted_eccentricity = pd.DataFrame({'Name':users.iloc[sorted_eccentricity_indexes].name.tolist(), 
                                      'Eccentricity': sorted_eccentricity_values, 
                                      'ml_target':users.iloc[sorted_eccentricity_indexes].ml_target.tolist()})

top_sorted_eccentricity.groupby('Eccentricity').count()['ml_target'].sort_values(ascending=False)
# top_sorted_eccentricity
```

<div class="output execute_result" data-execution_count="69">

    Eccentricity
    7     27399
    8      8677
    6      1113
    9       480
    10       27
    11        4
    Name: ml_target, dtype: int64

</div>

</div>

<div class="cell markdown">

Radius of Followers Graph

</div>

<div class="cell code" data-execution_count="16">

``` python
radius_of_graph = nx.radius(followers)
radius_of_graph
```

<div class="output stream stdout">

``` 
6
```

</div>

</div>

<div class="cell markdown">

Diameter of Followers Graph

</div>

<div class="cell code" data-execution_count="17">

``` python
diameter_of_graph = nx.diameter(followers)
diameter_of_graph
```

<div class="output stream stdout">

    11

</div>

</div>

<div class="cell markdown">

For verification diameter=maximum eccentricity

</div>

<div class="cell markdown">

Density of follower

</div>

<div class="cell code">

``` python
density_of_graph = nx.density(followers)
density_of_graph
```

<div class="output display_data">

    0.0004066878203117068

</div>

</div>

<div class="cell markdown">

Degree Distribution upto degree value 50

</div>

<div class="cell code" data-execution_count="9">

``` python
degree_distrubution = nx.degree_histogram(followers)
fig = plt.figure(figsize=(15, 10))
x = [i for i in range(0, 50)]
plt.bar(x, degree_distrubution[0:50])
plt.show()
```

<div class="output display_data">

![](9167945a8b29f7317424c7dfbc87ffe11284f5bb.png)

</div>

</div>

<div class="cell markdown">

Connected Components

</div>

<div class="cell code">

``` python
print(nx.is_connected(followers))
print(nx.number_connected_components(followers))
```

<div class="output stream stdout">

    True
    1

</div>

</div>

<div class="cell markdown">

Average Path Length

</div>

<div class="cell code">

``` python
average_short_path_length = nx.average_shortest_path_length(followers)
average_short_path_length
```

<div class="output display_data">

    3.2464090056353823

</div>

</div>

<div class="cell markdown">

-----

</div>

<div class="cell code" data-execution_count="41">

``` python
# Creating dataframe
single_value_calc = pd.DataFrame({'Radius': [radius_of_graph], 'Diameter': [diameter_of_graph], 'Density':[density_of_graph], 
                                  'Connected Component': [nx.number_connected_components(followers)],
                                  'Average Path Length': [average_short_path_length]})

# single_value_calc = pd.DataFrame({'Radius': [6], 'Diameter': [11],  'Density': [0.0004066878203117068], 
#                                   'Connected Component': [1], 'Average Path Length': [3.2464090056353823]})

single_value_calc.rename(index={0:'Overall Graph Measures'})
```

<div class="output execute_result" data-execution_count="41">

``` 
                        Radius  Diameter   Density  Connected Component  \
Overall Graph Measures       6        11  0.000407                    1   

                        Average Path Length  
Overall Graph Measures             3.246409  
```

</div>

</div>

<div class="cell code" data-execution_count="66">

``` python
local_clustering_sort = list((k,v) for k, v in sorted(local_cluster.items(), key=lambda item: item[0], reverse=True))
eccentricity_sort = list((k,v)for k, v in sorted(eccentricity.items(), key=lambda item: item[0], reverse=True))

# get index (node number) and value (node centrality value) top 10 after sorting
sorted_indexes = [x[0] for x in local_clustering_sort]
local_values_sort = [x[1] for x in local_clustering_sort]
eccentricity_values_sort = [x[1] for x in eccentricity_sort]

# Creating dataframe
eccentricity_and_local_cluster = pd.DataFrame({'Name':users.iloc[sorted_indexes].name.tolist(), 
                                      'Eccentricity': eccentricity_values_sort, 
                                      'Local Clustering': local_values_sort})

eccentricity_and_local_cluster
```

<div class="output execute_result" data-execution_count="66">

``` 
                 Name  Eccentricity  Local Clustering
0       caseycavanagh             7          0.333333
1            Injabie3             8          0.000000
2            qpautrat             7          0.000000
3           kris-ipeh             7          0.000000
4      shawnwanderson             8          0.000000
...               ...           ...               ...
37695    sunilangadi2             8          0.000000
37696       SuhwanCha             7          0.000000
37697     JpMCarrilho             8          0.000000
37698      shawflying             8          0.178571
37699          Eiryyy             8          0.000000

[37700 rows x 3 columns]
```

</div>

</div>

<div class="cell markdown">

-----

</div>

<div class="cell markdown">

### Centrality Measures

</div>

<div class="cell markdown">

Degree Centrality

</div>

<div class="cell code" data-execution_count="5">

``` python
# Applying degree centrality in NetworX
deg_centrality = nx.degree_centrality(followers)

# Applying degree centrality in NetworX
deg_centrality = nx.degree_centrality(followers)
# Sorting degree centrality and getting top 10
sorted_deg_centrality = list(dict(sorted(deg_centrality.items(), key=lambda item: item[1], reverse=True)).items())

# get index (node number) and value (node centrality value) top 10 after sorting
sorted_deg_centrality_indexes = [x[0] for x in sorted_deg_centrality]
sorted_deg_centrality_values = [x[1] for x in sorted_deg_centrality]

# Creating dataframe
top_degree_centrality = pd.DataFrame({'Name':users.iloc[sorted_deg_centrality_indexes].name.tolist(), 
                                      'Degree Centrality': sorted_deg_centrality_values, 
                                      'ml_target':users.iloc[sorted_deg_centrality_indexes].ml_target.tolist()})

top_degree_centrality
```

<div class="output execute_result" data-execution_count="5">

``` 
                    Name  Degree Centrality  ml_target
0           dalinhuang99           0.250882          0
1                 nfultz           0.187936          0
2             addyosmani           0.088172          0
3                Bunlong           0.078464          0
4      gabrielpconceicao           0.065466          0
...                  ...                ...        ...
37695    chrisryancarter           0.000027          0
37696          kcakdemir           0.000027          1
37697        chadmazilly           0.000027          0
37698       orionblastar           0.000027          1
37699       jovanidash21           0.000027          0

[37700 rows x 3 columns]
```

</div>

</div>

<div class="cell markdown">

Closeness Centrality

</div>

<div class="cell code" data-execution_count="7">

``` python
# Loading save json of closeness centrality output
# import pickle
# with open('closness_centrality.pkl', 'rb') as f:
#     closeness_centrality = pickle.load(f)

# Applying closeness centrality in NetworX
closeness_centrality = nx.closeness_centrality(followers)


# Sorting degree centrality and getting top 10
sorted_closness_centrality = list(dict(sorted(closeness_centrality.items(), key=lambda item: item[1], reverse=True)).items())

# get index (node number) and value (node centrality value) top 10 after sorting
sorted_closeness_centrality_indexes = [x[0] for x in sorted_closness_centrality]
sorted_closeness_centrality_values = [x[1] for x in sorted_closness_centrality]

# Creating dataframe
top_closeness_centrality = pd.DataFrame({'Name':users.iloc[sorted_closeness_centrality_indexes].name.tolist(), 
                                      'Closeness Centrality': sorted_closeness_centrality_values, 
                                      'ml_target':users.iloc[sorted_closeness_centrality_indexes].ml_target.tolist()})

top_closeness_centrality
```

<div class="output execute_result" data-execution_count="7">

``` 
                    Name  Closeness Centrality  ml_target
0                 nfultz              0.523081          0
1           dalinhuang99              0.517787          0
2                Bunlong              0.466324          0
3             addyosmani              0.450342          0
4      gabrielpconceicao              0.447461          0
...                  ...                   ...        ...
37695          haochenli              0.155371          0
37696      abhishekpopli              0.153934          1
37697          scandeiro              0.147439          0
37698          jazzchipc              0.145151          0
37699         SOUMAJYOTI              0.141389          1

[37700 rows x 3 columns]
```

</div>

</div>

<div class="cell markdown">

Betweenness Centrality

</div>

<div class="cell code" data-execution_count="8">

``` python
# Loading save json of closeness centrality output
# import pickle
# with open('betweeness_centrality.pkl', 'rb') as f:
#     betweeness_centrality = pickle.load(f)

# Applying betweeness centrality in NetworX
betweeness_centrality = nx.betweenness_centrality(followers)

# Sorting degree centrality and getting top 10
sorted_between_centrality = list(dict(sorted(betweeness_centrality.items(), key=lambda item: item[1], reverse=True)).items())

# get index (node number) and value (node centrality value) top 10 after sorting
sorted_between_centrality_indexes = [x[0] for x in sorted_between_centrality]
sorted_between_centrality_values = [x[1] for x in sorted_between_centrality]

# Creating dataframe
top_between_centrality = pd.DataFrame({'Name':users.iloc[sorted_between_centrality_indexes].name.tolist(), 
                                      'Betweeness Centrality': sorted_between_centrality_values, 
                                      'ml_target':users.iloc[sorted_between_centrality_indexes].ml_target.tolist()})

top_between_centrality
```

<div class="output execute_result" data-execution_count="8">

``` 
                    Name  Betweeness Centrality  ml_target
0           dalinhuang99               0.269599          0
1                 nfultz               0.240541          0
2                Bunlong               0.055323          0
3             addyosmani               0.043408          0
4      gabrielpconceicao               0.035337          0
...                  ...                    ...        ...
37695    chrisryancarter               0.000000          0
37696          kcakdemir               0.000000          1
37697        chadmazilly               0.000000          0
37698       orionblastar               0.000000          1
37699       jovanidash21               0.000000          0

[37700 rows x 3 columns]
```

</div>

</div>

<div class="cell code" data-execution_count="13">

``` python
plt.subplot(1,3,1)
plt.xlabel('Centrality Values')
plt.title('Degree Centrality')

top_degree_centrality['Degree Centrality'].plot.hist(figsize=(30, 5), ylim=(0,200))
plt.subplot(1,3,2)
plt.xlabel('Centrality Values')
plt.title('Closeness Centrality')

top_closeness_centrality['Closeness Centrality'].plot.hist(figsize=(30, 5))

plt.subplot(1,3,3)
plt.xlabel('Centrality Values')
plt.title('Betweeness Centrality')

top_between_centrality['Betweeness Centrality'].plot.hist(figsize=(30, 5))

plt.show()
```

<div class="output display_data">

![](62ff54b81842d9ed2b0adab8f469a59a43562cd5.png)

</div>

</div>

<div class="cell markdown">

Visualizing Highest Degree Centrality Node in Graph

</div>

<div class="cell code" data-execution_count="143">

``` python
followers_network = pd.read_csv('musae_git_edges.csv')
users = pd.read_csv('musae_git_target.csv')

nodes_with_deg_gret_100 = dict((k, v) for k, v in dict(followers.degree).items() if v > 50)

# Creating a function to check if id_1 or id_2 is ML or Web developer
def check_if_exist(id):
    if id in list(nodes_with_deg_gret_100.keys()):
        return 1
    else:
        return 0

followers_network['first_id'] = followers_network['id_1'].apply(check_if_exist)
# followers_network['second_id'] = followers_network['id_2'].apply(check_if_exist)
followers_test = followers_network[(followers_network['first_id'] == 1) & (followers_network['id_2'] == 31890)]
G = nx.from_pandas_edgelist(followers_test, 'id_1', 'id_2')

# Visualizing the highest degree centrality node regin in Graph
fig = plt.figure(figsize=(10, 10))
plt.margins(0,0)

nodes_sizes = []
nodes_color = []

for each in list(G.nodes):
    if each == sorted_deg_centrality[0][0]:
        nodes_sizes.append(4000)
        nodes_color.append('#f02b79')

    elif each == sorted_deg_centrality[1][0]:
        nodes_sizes.append(1000)
        nodes_color.append('#f02b79')
    else:
        nodes_sizes.append(20)
        nodes_color.append('#02A4DE')

print('Highest Degree Centrality Node Lies in Red Shaded Region')
nx.draw(G, node_size=nodes_sizes, node_color=nodes_color, edge_color='#dce8e0')
```

<div class="output stream stdout">

    Highest Degree Centrality Node Lies in Red Shaded Region

</div>

<div class="output display_data">

![](b4f6408d23d4806ef75203cd26280b60ce2d37d4.png)

</div>

</div>

<div class="cell markdown">

Visualizing Two Highest Closeness Centrality Node in Graph

</div>

<div class="cell code" data-execution_count="144">

``` python
followers_network = pd.read_csv('musae_git_edges.csv')
users = pd.read_csv('musae_git_target.csv')

nodes_with_deg_gret_100 = dict((k, v) for k, v in dict(followers.degree).items() if v > 50)

# Creating a function to check if id_1 or id_2 is ML or Web developer
def check_if_exist(id):
    if id in list(nodes_with_deg_gret_100.keys()):
        return 1
    else:
        return 0

followers_network['first_id'] = followers_network['id_1'].apply(check_if_exist)
# followers_network['second_id'] = followers_network['id_2'].apply(check_if_exist)
followers_test = followers_network[(followers_network['first_id'] == 1) & (followers_network['id_2'] == 31890)]
G = nx.from_pandas_edgelist(followers_test, 'id_1', 'id_2')

# Visualizing the highest degree centrality node regin in Graph
fig = plt.figure(figsize=(10, 10))
plt.margins(0,0)

nodes_sizes = []
nodes_color = []

for each in list(G.nodes):
    if each == sorted_closness_centrality[0][0]:
        nodes_sizes.append(4000)
        nodes_color.append('#f02b79')

    elif each == sorted_closness_centrality[1][0]:
        nodes_sizes.append(1000)
        nodes_color.append('#f02b79')
    else:
        nodes_sizes.append(20)
        nodes_color.append('#02A4DE')


print('Highest Closeness Centrality Node Lies in Red Shaded Region')
nx.draw(G, node_size=nodes_sizes, node_color=nodes_color, edge_color='#dce8e0')
```

<div class="output stream stdout">

    Highest Closeness Centrality Node Lies in Red Shaded Region

</div>

<div class="output display_data">

![](02b24e2bf484182a025382bcc69e5c572ae61274.png)

</div>

</div>

<div class="cell markdown">

Visualizing Two Betwenness Closeness Centrality Node in Graph

</div>

<div class="cell code" data-execution_count="145">

``` python
followers_network = pd.read_csv('musae_git_edges.csv')
users = pd.read_csv('musae_git_target.csv')

nodes_with_deg_gret_100 = dict((k, v) for k, v in dict(followers.degree).items() if v > 50)

# Creating a function to check if id_1 or id_2 is ML or Web developer
def check_if_exist(id):
    if id in list(nodes_with_deg_gret_100.keys()):
        return 1
    else:
        return 0

followers_network['first_id'] = followers_network['id_1'].apply(check_if_exist)
# followers_network['second_id'] = followers_network['id_2'].apply(check_if_exist)
followers_test = followers_network[(followers_network['first_id'] == 1) & (followers_network['id_2'] == 31890)]
G = nx.from_pandas_edgelist(followers_test, 'id_1', 'id_2')

# Visualizing the highest degree centrality node regin in Graph
fig = plt.figure(figsize=(10, 10))
plt.margins(0,0)

nodes_sizes = []
nodes_color = []

for each in list(G.nodes):
    if each == sorted_between_centrality[0][0]:
        nodes_sizes.append(4000)
        nodes_color.append('#f02b79')

    elif each == sorted_between_centrality[1][0]:
        nodes_sizes.append(1000)
        nodes_color.append('#f02b79')
    else:
        nodes_sizes.append(20)
        nodes_color.append('#02A4DE')


print('Highest Betweenness Centrality Node Lies in Red Shaded Region')
nx.draw(G, node_size=nodes_sizes, node_color=nodes_color, edge_color='#dce8e0')
```

<div class="output stream stdout">

    Highest Betweenness Centrality Node Lies in Red Shaded Region

</div>

<div class="output display_data">

![](0580f19c6e96630907dbb22a759ae259a7496744.png)

</div>

</div>
