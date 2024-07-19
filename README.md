# Momepy - Analyse des réseaux routiers et piétons

Momepy est une bibliothèque Python puissante pour l'analyse quantitative de la forme urbaine, avec un accent particulier sur l'analyse des réseaux routiers et piétons. Ce README se concentre sur l'utilisation de Momepy pour analyser le réseau piéton de Troyes, France, en mettant l'accent sur les mesures de centralité et la continuité des rues.

## Installation

```bash
pip install momepy osmnx geopandas matplotlib
```

## Fonctionnalités principales

### 1. Importation et préparation des données

```python
import geopandas as gpd
import momepy
import osmnx as ox
import matplotlib.pyplot as plt

streets_graph = ox.graph_from_place('Troyes, France', network_type='walk')
streets_graph = ox.projection.project_graph(streets_graph)
edges = ox.graph_to_gdfs(ox.get_undirected(streets_graph), nodes=False, edges=True,
                         node_geometry=False, fill_edge_geometry=True)
```

### 2. Conversion en format compatible Momepy

```python
primal = momepy.gdf_to_nx(edges, approach='primal')
```

### 3. Analyse de centralité

#### Closeness Centrality

```python
primal = momepy.closeness_centrality(primal, radius=400, name='closeness400', distance='mm_len', weight='mm_len')
```

#### Betweenness Centrality

```python
primal = momepy.betweenness_centrality(primal, name='betweenness_metric_n', mode='nodes', weight='mm_len')
```

### 4. Analyse de continuité des rues

```python
continuity = momepy.COINS(streets)
stroke_gdf = continuity.stroke_gdf()
```

### 5. Analyse de linéarité

```python
edg_lin = momepy.Linearity(edges)
edges['linearity'] = edg_lin.series
```

## Visualisation des résultats

### Closeness Centrality

```python
nodes = momepy.nx_to_gdf(primal, lines=False)
f, ax = plt.subplots(figsize=(35, 35))
nodes.plot(ax=ax, column='closeness400', cmap='Spectral_r', scheme='quantiles', k=15, alpha=0.6)
ax.set_axis_off()
ax.set_title('closeness400')
plt.show()
```

### Betweenness Centrality

```python
f, ax = plt.subplots(figsize=(35, 35))
nodes.plot(ax=ax, column='betweenness_metric_n', cmap='Spectral_r', scheme='quantiles', k=7, alpha=0.6)
ax.set_axis_off()
ax.set_title('betweenness_metric_n')
plt.show()
```

### Continuité des rues

```python
stroke_gdf.plot(stroke_gdf.length,
                figsize=(25, 25),
                cmap="viridis_r",
                linewidth=.5,
                legend=True,
                scheme="headtailbreaks"
               ).set_title("Continuité dans les réseaux de rues piétonnes de Troyes", fontsize=15)
```

### Linéarité des rues

```python
f, ax = plt.subplots(figsize=(25, 25))
edges.plot(ax=ax, column='linearity', legend=True, cmap='coolwarm_r', scheme='quantiles', k=6)
ax.set_axis_off()
ax.set_title("Linéarité dans les réseaux de rues piétonnes de Troyes", fontsize=15)
plt.show()
```

## Exemples d'utilisation

Le notebook fourni illustre l'analyse du réseau piéton de Troyes, démontrant :
- L'importation des données depuis OpenStreetMap
- L'analyse de la centralité de proximité (closeness centrality)
- L'analyse de la centralité d'intermédiarité (betweenness centrality)
- L'étude de la continuité des rues
- L'analyse de la linéarité des segments de rue

## Licence

Momepy est distribué sous licence BSD 3-Clause. Voir le fichier LICENSE pour plus de détails.

## Contact

Pour plus d'informations, visitez la [documentation officielle de Momepy](http://docs.momepy.org/).
