[![Build Status](https://travis-ci.org/tresata/spark-datasetops.svg?branch=master)](https://travis-ci.org/tresata/spark-datasetops)

# spark-datasetops
Spark-datasetops is a tiny library that aims to make Spark SQL Dataset more developer friendly by bringing back the operators we all love to use on key-value RDDs (the ones defined in PairRDDFunctions). I suppose these operators were nixed since they cannot easily be ported to Java and Python, but given how easy it is to bring them back it seems like a shame not to have them.

To use simply import RichPairDataset which is an implicit class:
```
import com.tresata.spark.datasetops.RichPairDataset
```

RichPairDataset adds methods to a any key-value Dataset (```Dataset[(K, V)]```). Some examples taken from unit tests:
```
scala> import spark.implicits._
scala> import com.tresata.spark.datasetops.RichPairDataset
scala> val x = Seq((1, 2), (1, 3), (2, 4)).toDS
scala> val y = Seq((1, "a"), (1, "b"), (2, "c")).toDS
scala> x.mapValues(_ + 1).show
+---+---+
| _1| _2|
+---+---+
|  1|  3|
|  1|  4|
|  2|  5|
+---+---+
scala> x.aggByKey(0, { (b: Int, a: Int) => b + a }, { (b1: Int, b2: Int) => b1 + b2 }, { (b: Int) => b }).show
+-----+-----------+
|value|anon$1(int)|
+-----+-----------+
|    2|          4|
|    1|          5|
+-----+-----------+
scala> x.countByKey.show
+-----+--------+
|value|count(1)|
+-----+--------+
|    2|       1|
|    1|       2|
+-----+--------+
scala> x.joinOnKey(y).show
+---+-----+
| _1|   _2|
+---+-----+
|  1|[2,b]|
|  1|[2,a]|
|  1|[3,b]|
|  1|[3,a]|
|  2|[4,c]|
+---+-----+
```

Enjoy!
Team @ Tresata
