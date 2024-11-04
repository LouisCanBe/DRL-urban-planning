# DRL urban planning

![Loading Model Overview](assets/pipeline_v3.png "Model Overview")
---

This repo contains the source codes and data for our paper:

Yu Zheng, Yuming Lin, Liang Zhao, Tinghai Wu, Depeng Jin, Yong Li,  **Spatial planning of urban communities via deep reinforcement learning**.

## Installation

### Environment

* **Tested OS:** Windows 10
* Python >= 3.8
* PyTorch >= 1.8.1

### Dependencies

1. Install [PyTorch](https://pytorch.org/get-started/previous-versions/) with the correct CUDA version.
2. Set the following environment variable to avoid problems with multiprocess trajectory sampling:

    ```
    export OMP_NUM_THREADS=1
    ```

## Data

The data used for training and evaluation can be found in [urban_planning/cfg/test_data](urban_planning/cfg/test_data).
We provide all the three scenarios used in our paper, including one synthetic grid community in [urban_planning/cfg/test_data/synthetic](urban_planning/cfg/test_data/synthetic), and two real-world communities, HLG and DHM, with and without planning concepts, in [urban_planning/cfg/test_data/real](urban_planning/cfg/test_data/real).
The data for the real-world communities are collected from the widely used [OpenStreetMap](https://www.openstreetmap.org/) (OSM) using [OSMnx](https://github.com/gboeing/osmnx).
For each case, we provide the following data:

* `init_plan.pickle`: the initial conditions of the community in [geopandas.GeoDataFrame](https://geopandas.org/en/stable/docs/reference/api/geopandas.GeoDataFrame.html) form, including the initial land blocks, roads, and junctions.
* `objectives.yaml`: the planning objectives (requirements) of the community in [yaml](https://yaml.org/) form, including the required number/area of different functionalities, and the minimum/maximum area of each land use type.

The figure below illustrates the initial conditions of the three scenarios.  
![Loading Data Overview](assets/initial_conditions.png "Initial Conditions")

With the initial conditions and planning objectives, the agent will generate millions of spatial plans for the community in real-time during training, which are stored in the replay buffer for training.  
  
用于训练和评估的数据可以在[urban_planning/cfg/test_data](urban_planning/cfg/test_data)中找到。
我们提供了本文中使用的所有三种场景，包括[urban_planning/cfg/test_data/synthetic](urban_planning/cfg/test_data/synthetic)中的一个合成网格社区，以及[urban_planning/cfg/test_data/real](urban_planning/cfg/test_data/real)中的两个真实世界社区，HLG 和 DHM，有和没有规划概念。
现实世界社区的数据是通过广泛使用的 [OpenStreetMap](https://www.openstreetmap.org/)+(OSM)+使用 [OSMnx](https://github.com/gboeing/osmnx) 收集的。
对于每种情况，我们提供以下数据：  

* `init_plan.pickle`：[geopandas.GeoDataFrame](https://geopandas.org/en/stable/docs/reference/api/geopandas.GeoDataFrame.html) 形式社区的初始条件，包括初始土地区块、道路和连接点。
* `objectives.yaml`：以 [yaml](https://yaml.org/)+形式列出的社区规划目标（要求），包括所需数量的不同功能区域，以及每种土地使用类型的最小和最大区域。

下图说明了三种情景的初始条件。  
![Loading+Data+Overview](assets/initial_conditions.png "初始条件")

有了初始条件和规划目标，代理将在训练期间实时为社区生成数百万个空间规划，这些规划存储在训练重放缓冲区中。

## Change base map 更换基础地图
原作团队在HLG和DHM社区上进行了实验，这些都是中国北京的真实世界社区。
你可以将基础地图更换为你自己的社区。
他们在[extra](extra)中提供了一个示例，展示如何为中国Huizhou的另一个社区准备基础地图数据。



## Training 训练模型  

### 训练模型

你可以使用 [urban_planning/cfg/exp_cfg/real](urban_planning/cfg/exp_cfg/real) 中提供的配置来训练自己的模型。

#### 示例：训练 HLG/jh 社区的模型

要训练 HLG/jh 社区的模型，请运行以下命令：

You can train your own models using the provided config in [urban_planning/cfg/exp_cfg/real](urban_planning/cfg/exp_cfg/real).  

For example, to train a model for the HLG community, run:

```python
py -m urban_planning.train --cfg hlg --global_seed 111

py -m urban_planning.train --cfg jh --global_seed 111
```

你可以将 `hlg` 替换为 `dhm`,`jh` 来训练 DHM 社区的模型。

#### 示例：训练具有规划概念的 HLG 社区模型

要训练具有规划概念的 HLG 社区模型，请运行以下命令：

You can replace `hlg` to `dhm` to train for the DHM community.  

To train a model with planning concepts for the HLG community, run:

```python
python3 -m urban_planning.train --cfg hlg_concept --global_seed 111
```  

你可以将 `hlg_concept` 替换为 `dhm_concept` 来训练具有规划概念的 DHM 社区模型。

#### 训练日志

一旦你开始训练我们的模型，你将看到以下日志：

You can replace `hlg_concept` to `dhm_concept` to train for the DHM community.  

You will see the following logs once you start training our model:

<https://user-images.githubusercontent.com/27959377/242561690-c01480a2-cedf-4889-8506-14add002227a.mp4>

## Visualization  

You can visualize the generated spatial plans using the provided notebook in [demo](demo).  
您可以使用[demo](demo)提供的notebook来可视化生成的空间计划

## Baselines  

### 评估基线模型

#### 集中式启发式算法评估

要评估集中式启发式算法，请运行以下命令：  
To evaluate the centralized heuristic, run:  

```python
python3 -m urban_planning.eval --cfg hlg --global_seed 111 --agent rule-centralized
```

#### 分散式启发式算法评估

要评估分散式启发式算法，请运行以下命令：  
To evaluate the decentralized heuristic, run:  

```python
python3 -m urban_planning.eval --cfg hlg --global_seed 111 --agent rule-decentralized
```

#### 几何集覆盖适应基线评估

要评估几何集覆盖适应基线，请运行以下命令：  
To evaluate the geometric set-coverage adapted baseline, run:  

```python
python3 -m urban_planning.eval --cfg hlg --global_seed 111 --agent gsca
```

#### GA 基线评估

要评估 GA 基线，请运行以下命令：  
To evaluate the GA baseline, run:

```python
python3 -m urban_planning.train_ga --cfg hlg --global_seed 111
python3 -m urban_planning.eval --cfg hlg --global_seed 111 --agent ga
```

你可以将 `hlg` 替换为 `dhm` 来评估 DHM 社区的模型。  
You can replace `hlg` to `dhm` to evaluate for the DHM community.

## License

Please see the [license](LICENSE) for further details.  
请参阅 [许可证](LICENSE) 以获取更多详细信息。
