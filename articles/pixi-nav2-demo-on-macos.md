---
title: "macosでnav2のgazebo demoをpixiで試す"
emoji: "😊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [ROS2, pixi, nav2, gazebo, macOS]
published: true
---
この記事では、macOS環境でROS2のナビゲーションスタック（nav2）とGazeboシミュレーションのデモを、パッケージマネージャーのpixiを使って動作させる方法を紹介します。  
従来、nav2やGazeboはLinux環境での利用が一般的ですが、macOSでもpixiを活用することでセットアップが簡単になりました。  
本記事では、セットアップ手順やデモの実行方法について、実際のコマンド例を交えながら解説します。

# pixiのinstall

```bash
curl -fsSL https://pixi.sh/install.sh | sh
```

# デモ用のprojectを作成

```bash
pixi init nav2_gazebo_demo -c conda-forge -c robostack-jazzy
cd nav2_gazebo_demo
wget https://raw.githubusercontent.com/prefix-dev/pixi/refs/heads/main/examples/ros2-nav2/pixi.toml -O pixi.toml # pixiのexampleのpixi.tomlをダウンロード
```

# nav2 gazebo simの立ち上げ

```bash
pixi run start-tb4
```

gazeboとrvizの画面が立ち上がるので、rviz2のinitial poseを指定すると、
自己位置推定が始まり、goal poseを指定すると経路計画を行なって自律移動が開始されます。
![gazebo](/images/pixi-nav2-demo-on-macos/gazebo.png)
![rviz](/images/pixi-nav2-demo-on-macos/rviz.png)

# 起動コマンドの解説

start-tb4はpixi.tomlのtaskとして記載されています。

```toml
[feature.jazzy.activation.env]
# Required for the RVIZ2 to properly startup on osx
RMW_IMPLEMENTATION = "rmw_cyclonedds_cpp"

[feature.jazzy.tasks]
start-tb4 = "ros2 launch nav2_bringup tb4_simulation_launch.py headless:=False"
```

macosのpixi環境でがfastddsが動かないためenvでcycloneddsを選択しています。

必要なdependenciesはcondaのrobostack-jazzyというchannelからinstallしています。

```toml
[feature.jazzy]
channels = ["https://prefix.dev/robostack-jazzy"]

[feature.jazzy.dependencies]
ros-jazzy-desktop = "*"
ros-jazzy-nav2-bringup = "*"
```
