# Pyspark Notebook extended with GraphFrames

I found that connecting [GraphFrames](https://github.com/graphframes/graphframes) to [pyspark](http://spark.apache.org/) inside a Jupyter notebook was trickier than I expected. This Dockerfile is the simplest way I found to get it to work. It is based on `jupyter/pyspark-notebook`, which seemed to be a reasonable starting point.

```
python 3.7
spark 2.4
graphframes 0.7.0
```

Build the image and take note of the `id` to run the container. Be sure to forward port `8888` when starting it:

```bash
docker build .
docker run -t --rm -p 8888:8888 <image-id>
```

The terminal output will contain the notebook url (`localhost:8888`) and a token. Visit the url in a browser and use the token to authenticate.

If everything goes will, the following minimalistic graph should build properly.

```python
from pyspark.sql import SparkSession
from graphframes import GraphFrame

session = SparkSession\
    .builder\
    .master('local')\
    .getOrCreate()

nodes = session.createDataFrame(
  [('1', 'Ada'), ('2', 'Bernd'), ('3', 'Claire')],
  ['id', 'name'])

edges = session.createDataFrame(
  [('1', '2'), ('2', '1'), ('1', '3')],
  ['src', 'dst'])

graph = GraphFrame(nodes, edges)
graph.inDegrees.show()
```
