# JsPsych实验教程

## 介绍
本教程将指导你如何使用JsPsych设计一个心理学实验，并使用Flexbox进行布局。

## 目录
1. [JsPsych简介](#JsPsych简介)
2. [准备工作](#准备工作)
3. [实验设计](#实验设计)
4. [Flexbox布局](#Flexbox布局)
5. [Markdown编写](#Markdown编写)
6. [Git版本控制](#Git版本控制)

## JsPsych简介
JsPsych是一个用于创建和运行心理学实验的JavaScript库。它具有丰富的插件和功能，适用于各种实验设计。目标读者为不熟悉JsPsych的人，教程将详细介绍每一步骤，确保读者能够按照教程完成实验设计。

## 准备工作
在开始之前请确保你已经安装了以下工具：
•	Node.js
•	Git
你可以通过以下链接下载和安装：
- [Node.js](https://nodejs.org/)
- [Git](https://git-scm.com/)

## 实验设计
### 实验任务
设计一个反应时间实验，参与者需要在看到刺激后尽快做出反应。

### 实现步骤
1. 引入JsPsych库和CSS文件
2. 设置实验时间线
3. 添加欢迎页面
4. 添加指导页面
5. 设置颜色数组并随机化顺序
6. 创建实验任务循环
7. 计算并显示实验结果
8. 初始化并运行实验

引入JsPsych库和CSS文件
首先，我们需要在HTML文件的头部引入JsPsych库和相关的CSS文件。我们还将引用一个外部的CSS文件 styles.css 用于自定义样式。
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Advanced JsPsych Reaction Time Experiment</title>
    <script src="https://cdn.jsdelivr.net/npm/jspsych@6.3.1/jspsych.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/jspsych@6.3.1/plugins/jspsych-html-keyboard-response.js"></script>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/jspsych@6.3.1/css/jspsych.css">
    <link rel="stylesheet" href="styles.css"> <!-- 引用外部CSS文件 -->
</head>
```<body>

设置实验时间线
实验时间线是一个数组，用于存储实验的各个阶段。我们将逐步向这个数组添加实验阶段。
```<script>
    // 实验时间线
    var timeline = [];

添加欢迎页面
欢迎页面是实验的第一个阶段，显示欢迎信息并等待参与者按任意键继续。
```// 欢迎页面
        var welcome = {
            type: "html-keyboard-response",
            stimulus: "<p>欢迎参加实验。</p><p>按任意键继续。</p>"
        };
        timeline.push(welcome);

添加指导页面
指导页面向参与者解释实验任务，并等待他们按任意键开始实验。
```// 指导页面
        var instructions = {
            type: "html-keyboard-response",
            stimulus: "<p>在屏幕上会出现一个红色方块。</p><p>当方块变成其他颜色时，请尽快按下空格键。</p><p>按任意键开始。</p>"
        };
        timeline.push(instructions);

设置颜色数组并随机化顺序
我们定义一个颜色数组，并使用JsPsych的随机化函数来打乱颜色顺序。
```// 颜色数组
        var colors = ['green', 'blue', 'yellow', 'purple', 'orange'];
// 随机化颜色顺序
        colors = jsPsych.randomization.shuffle(colors);

创建实验任务循环
我们将创建一个循环，依次显示红色方块并在随机时间后变为其他颜色。参与者需要尽快按下空格键。
```// 实验任务循环
        for (var i = 0; i < colors.length; i++) {
            var trial = {
                type: "html-keyboard-response",
                stimulus: function(){
                    return "<div id='stimulus-container'><div class='stimulus' style='background-color:red;'></div></div>";
                },
                choices: jsPsych.NO_KEYS,
                trial_duration: function(){
                    return Math.floor(Math.random() * 2000) + 1000; // 随机1到3秒
                },
                on_finish: function(data){
                    jsPsych.data.addDataToLastTrial({
                        color: colors[i]
                    });
                }
            };
            timeline.push(trial);

            var response_trial = {
                type: 'html-keyboard-response',
                stimulus: function(){
                    return "<div id='stimulus-container'><div class='stimulus' style='background-color:" + colors[i] + ";'></div></div>";
                },
                choices: [' '],
                on_finish: function(data){
                    data.correct = data.rt > 0; // 记录反应时间
                }
            };
            timeline.push(response_trial);
        }

计算并显示实验结果
我们将计算参与者的平均反应时间和错误率，并在实验结束时显示结果。
```// 计算并显示实验结果
        var summary = {
            type: "html-keyboard-response",
            stimulus: function(){
                var trials = jsPsych.data.get().filter({correct: true});
                var rt = trials.select('rt').mean();
                var error_rate = (colors.length - trials.count()) / colors.length * 100;
                return "<p>实验结束。</p><p>平均反应时间：" + Math.round(rt) + "毫秒。</p><p>错误率：" + Math.round(error_rate) + "%。</p><p>按任意键退出。</p>";
            }
        };
        timeline.push(summary);

初始化并运行实验
最后，我们初始化并运行实验。
```// 启动实验
        jsPsych.init({
            timeline: timeline
        });
    </script>
</body>
</html>```

## Flexbox布局
Flexbox是一种CSS布局模式，能够更加灵活地排列页面元素。我们将使用Flexbox来布局实验页面，使得刺激物在屏幕上居中显示。

### 设置Flex容器
首先，我们将 body 元素设置为Flex容器，并使其内容居中对齐。
body {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh; /* 使容器高度占满整个视口 */
    margin: 0; /* 移除默认的body边距 */
}

### 设置刺激物容器
接下来，我们将 #stimulus-container 设置为Flex容器，使其中的刺激物居中对齐。
#stimulus-container {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%; /* 宽度占满父容器 */
    height: 100%; /* 高度占满父容器 */
}

### 设置刺激物
最后，我们为刺激物 .stimulus 设置固定的宽度和高度。
.stimulus {
    width: 100px; /* 刺激物宽度 */
    height: 100px; /* 刺激物高度 */
}

### 保存style.css文件，确保在HTML文件中引用了这个CSS文件
