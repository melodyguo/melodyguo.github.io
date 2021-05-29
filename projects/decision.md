# Decision Trees

I did some work with decisions trees to analyze trends in patient data. Since the patient data is classified, here is some sample code that I ran on a public data set.

```markdown
from sklearn import tree
import graphviz
import pandas as pd

# process data from file
def process_txt(filename, target_loc):
    file = open (filename, "r")
    contents = file.readlines()
    print("contents", len(contents))

    data = []
    target = []

    for line in contents:
        if "?" in line: # take out incomplete data
            continue
        temp = line.split(",")
        if target_loc == "beg":
            data.append(temp[1:])
            target.append(temp[0])
        elif target_loc == "end":
            data.append(temp[:len(temp)-1])
            target.append(temp[len(temp)-1].rstrip())

    return data, target

def process_csv(filename):
    file = pd.read_csv(filename)
    return file

# create decision tree using input data    
def make_that_tree(dot_name, data, target, var_names, clas_names):
    clf = tree.DecisionTreeClassifier()
    clf = clf.fit(data, target)
    dot_data = tree.export_graphviz(clf, out_file=dot_name,
                                feature_names= var_names,
                                class_names = clas_names,
                                filled=True, rounded=True,
                                special_characters=True)
    graph = graphviz.Source(dot_data)
    return clf
```

I ran iris.data and wine.data through my decision tree to get these results.
```
data, target = process_data("iris.data", "end")
make_that_tree("iris.dot", data, target,
                     ["sepal length in cm", "sepal width in cm",
                      "petal length in cm ", "petal width in cm"],
                     ["Iris Setosa", "Iris Versicolour", "Iris Virginica"])
```
![Image]()

```
data, target = process_data("wine.data", "beg")
make_that_tree("wine.dot", data, target,
                     ["Alcohol", "Malic acid", "Ash", "Alcalinity of ash",
                      "Magnesium", "Total phenols", "Flavanoids",
                      "Nonflavanoid phenols", "Proanthocyanins",
                      "Color intensity", "Hue", "OD280/OD315 of diluted wines",
                      "Proline"],
                     ["Wine 1", "Wine 2", "Wine 3"])
```
![Image]()
