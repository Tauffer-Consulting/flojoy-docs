---
sidebar_position: 4
id: creating-custom-node
title: Creating a custom node
---

## Division: an example

Suppose we wanted to contribute a node that divides two items elementwise (for the case of vector inputs, for instance). Although we could do this with the built-in `invert` and `multiply` nodes, we want to create this node for convenience.

### Creating the source files

To start, we create the file `DIVIDE/DIVIDE.py` inside [`/PYTHON/nodes/TRANSFORMERS/ARITHMETIC/`](https://github.com/flojoy-io/nodes/tree/main/TRANSFORMERS/ARITHMETIC). Each node must have its own folder.

We can then create our new function using the features discussed [here](../data-container).

```python {title='DIVIDE.py'}
import numpy as np
from flojoy import flojoy, OrderedPair

@flojoy(node_type="ARITHMETIC")
def DIVIDE(a: OrderedPair, b: OrderedPair) -> OrderedPair:
    x = a.x
    result = np.divide(a.y,b.y)
    return OrderedPair(x=x, y=result)
```

Note: The type hints are important! This is how Flojoy differentiates between node inputs (that you connect edges to) and parameters (that you can set in the node parameters panel). Anything that inherits from `DataContainer` (ex: `OrderedPair`, `Matrix`, etc.) is an input, and everything else is a parameter.

The `node_type` argument to the `flojoy` decorator specifies what kind of node this should display as in the frontend.

### A more advanced example

Let's say we want to create a node to wrap `scikit-learn`'s `train_test_split` function. This node will have to return two different `DataContainer`s.

```python {title="TRAIN_TEST_SPLIT.py"}

import pandas as pd
from typing import cast
from dataclasses import dataclass
from flojoy import flojoy, DataFrame
from sklearn.model_selection import train_test_split


@dataclass(frozen=True)
class TrainTestSplitOutput:
    train: DataFrame
    test: DataFrame


@flojoy(deps={"scikit-learn": "1.2.2"})
def TRAIN_TEST_SPLIT(
    default: DataFrame, test_size: float = 0.2
) -> TrainTestSplitOutput:
    df = default.m

    train, test = cast(list[pd.DataFrame], train_test_split(df, test_size))
    return TrainTestSplitOutput(train=DataFrame(df=train), test=DataFrame(df=test))
```

First, what if the node needs to import that isn't already installed? We can specify this in the `deps` argument to the `flojoy` decorator. This will ensure that the library is installed before the node is run.

Second, the node needs to return two `DataContainers`. We can do this by creating a dataclass with the names of the outputs as fields. Then, we return an instance of this dataclass.

Looking at the parameters, we have one `DataContainer` input, named `default`. When we only have 1 input and we don't want to label it in the frontend, we can name it `default`, which is a special name that Flojoy recognizes. This node also has a `test_size` parameter, that has a default value of 0.2.

### Creating Custom Component ( Frontend )

In Flojoy, you can create custom component for newly created nodes (i.e. shape and node connections). The custom components are located in [`/src/feature/flow_chart_panel/components/custom-nodes`](https://github.com/flojoy-io/studio/tree/main/src/feature/flow_chart_panel/components/custom-nodes) folder. Create a custom component for the newly created nodes and register the design in [`/src/configs/NodeConfigs.ts`](https://github.com/flojoy-io/studio/blob/main/src/configs/NodeConfigs.ts) file. In this case, its a `ARITHMETIC` type node, so you register custom component as `ARITHMETIC: YOUR_CUSTOM_COMPONENT`.
If you don't register the newly created node type,it will render the `DefaultNode` component.

```typescript {title='NodeConfigs.ts'}
import MyCustomComponent from '@src/feature/flow_chart_panel/components/custom-nodes/YOUR_CUSTOM_COMPONENT';

export const nodeConfigs = {
  default: DefaultNode,
  ARITHMETIC: MyCustomComponent,
};
```

### Registering the new function with Flojoy

:::info
This is now performed at startup of Flojoy.
:::

To update the databases with the functionalities of the nodes (including your new custom node), run the following in the root directory:

```bash
python3 write_python_metadata.py
```

### Almost done! Housekeeping time

Let's make sure your code is properly formatted!

We use [black](https://github.com/psf/black) as our formatter for Python, which you can install by running

```bash
pip3 install black
```

or

```bash
pip3 install -r requirements.txt
```

on the root of the nodes repo.

Once the formatter is installed, simply run

```bash
black .
```

on the root of the nodes repo and all your Python files will be properly formatted!

:::tip
It is always a good idea to setup format on save on the editor of your choice!
:::

### Congratulations! You've created your first custom node.

When creating custom nodes, make sure to go through the following steps:

- [x] Did I make my new function correctly?
  - [x] Did I add the `flojoy` decorator to my function?
  - [x] Did I pass two arguments to my function, the `DataContainer` inputs and the parameters `params` from the manifest?
- [x] Did I create a manifest file, correctly adding the correct category key?
- [x] Did I generate the manifest for the node?
- [x] Did I update the Python metadata?

### Common Errors:

- `[2023-05-17 08:29:33.105-RQ-watch] AttributeError: module 'nodes.GENERATORS.SIMULATIONS.TESTING.TESTING' has no attribute 'TESTING'`

This likely means your function name does not match the Key in your manifest.yaml file.

- `[2023-05-17 08:59:25.876-RQ-watch] cmd = node["cmd"]    KeyError: 'cmd'`

This likely means you have to run `python3 generate_manifest.py` in the root Flojoy directory.
