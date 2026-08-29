# WRC2026 具身智能 System 0/1/2 架构与参展厂商模型洞察

生成日期：2026-08-29  
范围说明：本报告聚焦 2026 世界机器人大会（WRC 2026，2026-08-19 至 2026-08-23，北京亦庄）及厂商在大会前后公开披露的具身模型、大小脑架构、端侧部署、算力和频率信息。WRC 官方披露参展规模为 300 余家企业、3000 余件展品；下表优先覆盖公开披露了“具身模型/大脑/小脑/VLA/世界模型/数据闭环”的机器人厂商。未公开信息统一标注为“未披露”，不以行业常见配置补猜。

## 1. System 0/1/2 框架定义

| 层级 | 在具身智能中的含义 | 典型模型/模块 | 典型频率 | 关键约束 |
|---|---|---|---|---|
| System 0 | 反射、本体稳定、伺服闭环、碰撞/跌倒恢复、局部安全约束，可类比“小脑+脊髓反射”。 | 电机控制器、MPC/WBC、RL 运控策略、全身控制基础模型、力控/触觉闭环。 | 电机/力矩环通常 100Hz 至 kHz；公开披露的学习控制模型常见 50Hz 级。 | 延迟、可靠性、能耗、安全性优先，通常必须端侧/本体内运行。 |
| System 1 | 快速感知到动作的技能策略，负责抓取、导航、分拣、上下料、跟随、短程操作；可类比“快思考”。 | VLA、端到端导航/抓取模型、动作专家、低层技能库、行为克隆/RL 策略。 | 常见 5Hz 至 50Hz；公开披露个例包括 10Hz-20Hz 端侧推理、50Hz 运控闭环。 | 需要在低延迟下泛化到物体、光照、位置、材质变化。 |
| System 2 | 慢速理解、规划、预测与交互，负责任务拆解、世界状态理解、物理后果预测、对话和记忆；可类比“大脑”。 | LLM/VLM、世界模型、WAM、任务规划 Agent、行业垂直大模型。 | 秒级到数 Hz；部分端侧轻量世界模型开始追求实时。 | 参数、显存、数据规模和训练算力是主要瓶颈；可端云协同。 |

## 2. 总体判断

1. WRC2026 的共同变化是从“能动/会表演”转向“能干活/可交付”。工业、物流、零售、巡检和家庭样板间成为展示主轴。
2. 头部厂商正在把“大小脑”从隐喻做成架构：System 0 低延迟运控模型、System 1 VLA/技能策略、System 2 世界模型/任务 Agent 分层协作。
3. “端侧部署”成为真实作业的门槛。优必选明确把高实时性能力压到端侧，云端承担慢推理、多机调度和模型管理；银河通用开源小脑支持 Jetson Orin onboard deployment；NVIDIA 也在 2026 年强调 Jetson Thor/Thor T3000 面向端侧基础模型。
4. 参数公开仍不充分。银河通用的 LDA-1B、AstraBrain-WBC 0.5，优必选 Thinker，智元 GO-1/ViLLA，BabyAlpha A3 的 7B 本地大模型，是少数给出参数或较具体部署线索的案例。
5. 频率公开更少。明确披露的频率/延迟包括：银河通用 AstraBrain-WBC 0.5 在 RTX 4090 上低于 1.5ms 推理、满足 50Hz 实时闭环；智元 GO-1/GO-2 业内报道为 10Hz 至 20Hz 端侧推理；优必选披露 VLA 端侧优化后显存需求从 64GB 降至 32GB，但未给出 Hz。

## 3. 重点厂商模型与 System 0/1/2 拆解表

| 厂商 | WRC2026 展示/近期披露 | System 0：小脑/运控 | System 1：VLA/技能 | System 2：大脑/世界模型/Agent | 部署位置与方式 | 参数量 | 算力 | 推理/交互频率 | 主要出处 |
|---|---|---|---|---|---|---|---|---|---|
| 优必选 UBTECH | WRC 展示 Cruzr S2/Y1 工业产线、Walker C1、U1；36Kr 称其体系为“foundation model - world model - action model”。 | 未披露独立小脑模型名称；工业机器人强调高精度谐波关节、力觉/视觉传感与端侧控制。 | Thinker-VLA，负责从任务理解到连续控制；WRC 展示汽车上下料、分拣等，抓取节拍最高接近 1100 件/小时。 | Thinker 基座模型、Thinker-WM 世界模型；U1 搭载 Resonance-LM 情感大模型。 | 高实时能力端侧；云端做慢推理、多机调度、模型管理。VLA 端侧全功能模块 GPU 显存需求由 64GB 优化至 32GB。 | Thinker 最大基座模型 100B；Thinker-VLA 参数未披露。 | 报道称工业作业型机器人需要 NVIDIA Thor 级端侧算力；NVIDIA 官方称优必选通过 Jetson 内存优化可从 Orin 64GB 迁移至 32GB。 | Hz 未披露；VLA 推理效率披露提升 176%。 | QbitAI: https://www.qbitai.com/2026/08/477253.html；36Kr: https://eu.36kr.com/en/p/3953394550537606；NVIDIA: https://blogs.nvidia.cn/blog/jetson-thor-robotics-edge-ai-agent/ |
| 银河通用 Galbot | WRC 官方展商页披露 AstraBrain、AstraBrain WAM、AstraBrain WBC、GraspVLA、GroceryVLA、TrackVLA；发布 ET1/星仔。 | AstraBrain-WBC 0.5 通用小脑，面向全身实时运控；开源 Humanoid-GPT 支持 Unitree G1 29DOF。 | GraspVLA、GroceryVLA、TrackVLA；零售/工业全程自主推理、无遥操。 | AstraBrain WAM；LDA-1B 将世界模型与动作模型统一，联合学习策略、前向动力学、逆向动力学和视觉预测。 | 小脑开源实现含 real-robot deployment，并有 onboard_deploy（Jetson Orin）。LDA-1B 支持跨本体后训练，生产部署算力未披露。 | LDA-1B 为 1B 参数；AstraBrain-WBC 0.5 为 8040 万参数。 | WBC 公开测试使用单张 RTX 4090；onboard 路径为 Jetson Orin。 | WBC 工程优化后 RTX 4090 上端到端推理 <1.5ms，可满足 50Hz 闭环。 | WRC 官方: https://www.worldrobotconference.com/expo/company/434.html；LDA-1B arXiv: https://arxiv.org/abs/2602.12215；Humanoid-GPT: https://github.com/GalaxyGeneralRobotics/Humanoid-GPT；QbitAI WBC: https://www.qbitai.com/2026/06/436813.html |
| 智元机器人 AGIBOT | 2026 年持续披露 AGIBOT WORLD 2026 数据集；近期披露 GO-1/ViLLA，WRC 期间参与具身场景活动。 | 产品体系称“三智合一”：运动智能、交互智能、操作智能；独立 System 0 模型参数未披露。 | GO-1 基于 Vision-Language-Latent-Action（ViLLA），以 Latent Planner 和 Action Expert 连接视频理解与动作执行。 | VLM 负责场景/语言理解；AGIBOT WORLD 2026 Theme 2 面向世界模型、神经仿真、物理感知数据。 | GO-1 支持部署到多款机器人本体；具体端侧芯片、显存和云边分工未披露。 | GO-1 参数未公开。 | 未披露；行业报道提到 GO-1/GO-2 百亿参数以内模型可在边缘端 10Hz-20Hz 推理，应视为二手口径。 | 官方未披露；二手报道为 10Hz-20Hz 端侧推理。 | AGIBOT Theme 2: https://agibot.com/article/231/detail/72.html；上观 GO-1: https://www.shobserver.cn/wx/detail.do?id=873140；AGIBOT World: https://agibot-world.com/ |
| 智平方 AI2 Robotics | WRC 官方展商页披露 AlphaBrain 和 AlphaBot 2；财联社披露 GOVLA/FiS-VLA 路线。 | 与全身动作/控制深度结合，但独立小脑模型名称和频率未公开。 | GOVLA 系列，2025 年推出 GOVLA 0.5（FiS-VLA），公开口径为“异构输入+异步频率”双系统 VLA。 | AlphaBrain 作为全栈自研具身大模型核心；强调原生面向真实物理世界。 | AlphaBot 2 由 AlphaBrain 定义形态并驱动；具体端侧/云端部署未披露。 | 未披露。 | 未披露。 | “异步频率”披露为架构特征，具体 Hz 未披露。 | WRC 官方: https://www.worldrobotconference.com/expo/company/409.html；财联社: https://www.cls.cn/detail/2341170 |
| 星海图 Galaxea | WRC 官方展商页展示 Kengo、R1 Pro/Lite；2026-06 发布 G0.5、Fast-WAM、全身控制基础模型。 | 高性能运动小脑、全身控制基础模型；Kengo 以运动小脑和具身大脑为核心。 | G0.5 VLA 基础模型，面向作业智能；官方称已开源。 | Fast-WAM 世界模型；三层智能观：本能智能、作业智能、进化智能。 | R1 Lite 标配高算力计算平台，原生支持端侧模型部署；Kengo 后续搭载具身大脑模型。 | 未披露。 | “高算力计算平台”但芯片/算力未披露。 | 未披露。 | WRC 官方: https://www.worldrobotconference.com/expo/company/446.html；21 经济: https://www.21jingji.com/article/20260617/herald/9e36e33f25a99595a8e3e3e6ff5e6f2d.html |
| 自变量机器人 X Square Robot | WRC 官方新闻页/公司官网显示 2026-08-19 亮相 WRC，面向家庭与物流；2026 发布 WALL-B、Wall-OSS-0.5。 | 未披露独立小脑；强调自研核心零部件和硬件本体原生适配基础模型控制。 | 端到端模型驱动通用机器人；Wall-OSS-0.5 直接把未任务微调的预训练模型搬上真实机器人测试 17 个任务。 | WALL-B 被公司定义为“世界统一模型”，强调取消视觉、语言、动作三方分离。 | 模型上真机；家庭/物流真实场景落地。具体端侧/云端分工未披露。 | 未披露。 | 未披露。 | 未披露。 | 公司官网: https://x2robot.com/news；WRC 复盘: https://finance.sina.cn/stock/jdts/2026-08-26/detail-iniprumq5643660.d.html |
| 北京人形机器人创新中心 | WRC 亮点展品“天工 Omni”；报道称天工巡检采用“慧思开物”平台和行业垂直大模型。 | 天工系列以双足/全身控制见长；巡检方案称“大脑+小脑”协同。 | 巡检路线规划、高压电控柜检测等行业技能。 | “慧思开物”具身智能平台 + 行业垂直大模型。 | 端侧/云端具体分工未披露；面向高危巡检实测验证。 | 未披露。 | 未披露。 | 未披露。 | WRC 亮点: https://www.worldrobotconference.com/208/；腾讯云转载: https://cloud.tencent.com/developer/article/2731996?policyId=1003 |
| 影身智能 Shadow AI | WRC 展示原生 4D 基座世界模型“破界 S1”和折纸盒柔性制造。 | 机械臂轨迹、力控实时调整等由控制层支撑，具体模型未披露。 | 折纸盒、涂胶、压底等柔性制造操作策略。 | 破界 S1 通过统一时空表征学习物体空间结构、动作约束与时序变化，用于重建、预测、决策。 | “影身360”用家用级 RGB 摄像头与消费级 GPU 从多视点实时合成 4D 数据；生产推理部署未披露。 | 未披露。 | 数据采集侧公开为消费级 GPU；训练/推理算力未披露。 | “实时 4D 数采”披露，但模型推理 Hz 未披露。 | 中国日报: https://cn.chinadaily.com.cn/a/202608/25/WS6a8d03aee4b09dff9814edff.html |
| 章鱼动力 Octopus | WRC 推出“脑-手-数据”一体化方案。 | OctoH-Hand 高自由度仿生灵巧手承担执行端。 | 人类操作数据向机器人本体迁移，构成感知-决策-执行闭环。 | SYNWorld 通用世界基础模型承担机器人认知推演，学习现实物理规律、跨场景迁移。 | 与 OctoSense 具身数据采集系统闭环；具体端侧部署未披露。 | 未披露。 | 未披露。 | 未披露。 | 腾讯云转载: https://cloud.tencent.com/developer/article/2731996?policyId=1003 |
| 蔚蓝智能 BabyAlpha | WRC 展出 BabyAlpha A3 四足机器狗。 | 四足运控未披露模型细节。 | 本地交互、跟随、环境识别。 | 70 亿参数本地运行大模型，支持自然语言对话、环境识别。 | 本地端侧运行，无需云端持续传输数据。 | 7B。 | 终端算力，芯片型号未披露。 | 未披露。 | 腾讯云转载: https://cloud.tencent.com/developer/article/2731996?policyId=1003 |
| 傅利叶智能 Fourier Intelligence | WRC 展示 GR-3/“具身之家”，自然语言控制多台机器人完成居家任务。 | 框架连接低级运动控制；具体小脑模型未披露。 | 具身模型、导航与规划系统协同执行取送、安防、接待等任务。 | LLM + embodied models + planning。 | 提供连接 LLM、具身模型、导航规划和低级运控的框架，让模型开发者接入验证。 | 未披露。 | 未披露。 | 未披露。 | KrASIA/36Kr: https://kr-asia.com/at-world-robot-conference-2026-robots-face-the-test-of-real-work；上观: https://www.shobserver.cn/news/detail?id=1162762 |
| 逐际动力 LimX Dynamics | WRC 亮点展品 LimX Luna；强调语音大模型对话、手势识别、姿态模仿、动作/视觉/语音原子化。 | 高动态本体、跑跳空翻能力，底层运控模型未披露。 | 姿态模仿、舞蹈/动作原子组合。 | 语音大模型对话、AI 自动解析客户需求生成场景模式。 | 平板端群控；可接入其他机器人与智能体。 | 未披露。 | 未披露。 | 多机同步/差异化表演动作同步误差 <50ms；模型推理频率未披露。 | WRC 亮点: https://www.worldrobotconference.com/208/ |
| 宇树 Unitree | WRC 期间王兴兴演讲指出泛化不足是瓶颈；G1/H1/R1 等本体和格斗赛热度高。 | 公开产品强调模仿与强化学习驱动、毫秒级响应控制；无 WRC 披露的大脑模型名称。 | 未公开自研 VLA 量产模型；公司平台含 UniFolm/UniPlatform。 | 王兴兴称主流路线为 VLA 和世界模型，并称公司 2025 年重启并扩大视频生成式世界模型投入。 | G1 官方基础算力为 8 核高性能 CPU；EDU/开发版本可选高算力模组。 | 未披露。 | G1 官方基础算力：8 核高性能 CPU；部分开发版本可选 Jetson Orin，具体 WRC 本体配置未披露。 | 官方未披露；第三方 H1 论文在 Unitree H1 上部署 actor+encoder 可 50Hz 控制，不能等同于宇树量产模型。 | Unitree G1: https://www.unitree.com/g1；WRC 预热: https://www.worldrobotconference.com/news/latestnews/2997.html；演讲报道: https://news.qq.com/rain/a/20260820A08S9A00 |

## 4. 算力与端侧部署要点

| 平台/厂商 | 公开算力信息 | 与 System 0/1/2 的关系 | 出处 |
|---|---|---|---|
| NVIDIA Jetson/IGX T3000 | 865 FP4 TFLOPS，32GB LPDDR5X，273GB/s 内存带宽；T2000 为 400 FP4 TFLOPS、16GB 内存；NVIDIA 称 Jetson 覆盖 70 TOPS 至 2000 TFLOPS。 | 面向端侧运行 LLM、VLM、VLA、世界基础模型；支撑 System 1/2 下沉到机器人本体。 | https://blogs.nvidia.cn/blog/jetson-thor-robotics-edge-ai-agent/ |
| NVIDIA Cosmos 3 Edge | 40 亿参数，兼容 Thor，端侧推理用于感知、实时推理、预测并生成动作。 | 是 System 2/1 融合式轻量世界基础模型，可做端侧世界模型与策略执行。 | https://blogs.nvidia.cn/blog/jetson-thor-robotics-edge-ai-agent/ |
| 黑芝麻智能 WRC 算力矩阵 | Kalos 最高 48 TOPS；Aura 70 TOPS；Liora 最高 700 TOPS，支持世界模型与端到端控制。 | 从 System 0/1 到 System 2 形成算力阶梯，尤其 Liora 指向人形和多机器人协同。 | https://wap.eastmoney.com/a/202608223850183143.html |
| 摩尔线程 WRC 主论坛观点 | 未来视觉模型、世界模型、VLA 将依赖千卡/万卡级集群和更大参数；应打通 AI 计算、3D 渲染、物理仿真、云边端一致架构。 | 指向 System 2 训练扩容与 System 1/2 端侧部署之间的矛盾。 | https://www.worldrobotconference.com/news/3609.html |

## 5. 架构洞察

### 5.1 真正分化不在“有无大模型”，而在大模型是否被拆成可部署系统

2026 年公开材料里，几乎所有头部厂商都在讲大模型，但架构成熟度不同：

- 优必选和银河通用最明确：分别披露“基座模型/世界模型/VLA”和“AstraBrain WAM/WBC/VLA”的多层模型体系。
- 智平方、星海图、自变量更强调端到端或统一模型路线，把感知、语言、动作放在一个更紧耦合的网络或框架中。
- 傅利叶更像平台型控制栈，把外部大模型/具身模型/导航规划/低级运控接起来，降低模型和本体适配成本。

### 5.2 System 0 正在从传统控制器升级为“运动基础模型”

银河通用 AstraBrain-WBC 0.5 是目前披露最完整的小脑案例：8040 万参数、2B 帧/约 2 万小时人类动作数据、因果 Transformer、RTX 4090 上 <1.5ms 推理、50Hz 闭环。它说明 System 0 不再只是手写控制器或单技能 RL policy，而可能成为可扩展的运动语料库模型。

### 5.3 System 1 的商业价值来自“少换型、少标定、少遥操”

WRC 展示中，工业和物流场景最关心的不是一次性成功，而是换工件、换料箱、换光照后的成功率、人工接管率和并线时间。Galbot 的 GraspVLA/GroceryVLA、优必选 Thinker-VLA、智平方 GOVLA、自变量 WALL-B 都围绕这一问题展开。

### 5.4 System 2 正从语言规划走向物理预测

世界模型/WAM/4D 世界模型成为 WRC2026 后半场关键词。优必选 Thinker-WM、银河通用 LDA-1B、影身破界 S1、章鱼动力 SYNWorld、自变量 WALL-B 都在尝试让机器人理解动作后果，而不是只把语言指令翻译成动作。

### 5.5 端侧算力是 System 1/2 落地的实际边界

优必选的端侧优化数据很有代表性：端侧全功能模块 GPU 显存需求从 64GB 降到 32GB，才接近可装进机器人本体的工程边界。NVIDIA 2026 年发布 T3000/T2000 和 Cosmos 3 Edge，也说明行业正在把大脑的一部分推到端侧，而不是所有复杂推理都依赖云端。

## 6. 信息缺口与后续跟踪清单

1. 大多数厂商未披露模型参数、显存、芯片型号和推理 Hz，尤其是智平方 AlphaBrain/GOVLA、星海图 G0.5/Fast-WAM、自变量 WALL-B、傅利叶 GR-3 框架。
2. “端侧运行”需要区分本体内板卡、近端工控机、展台局域网边缘服务器和云端服务。公开报道常混用，需要现场或厂商白皮书进一步确认。
3. 许多 WRC 演示仍可能包含脚本、遥操或场景预设。应重点追踪：人工接管率、连续工作小时数、失败恢复策略、换型时间、有效数据回流比例。
4. 小脑模型与大脑模型的接口仍缺统一标准。傅利叶的通用控制栈、开放原子/工信部标准化、AgiBot/银河/星海图的开源路线值得持续观察。
5. 未来 6 至 12 个月的关键证据不是发布会，而是客户现场：同一模型能否跨客户、跨本体、跨任务复用，并把单位任务成本降到 ROI 为正。

## 7. 来源索引

- WRC 2026 官方亮点展品：https://www.worldrobotconference.com/208/
- WRC 2026 银河通用展商页：https://www.worldrobotconference.com/expo/company/434.html
- WRC 2026 智平方展商页：https://www.worldrobotconference.com/expo/company/409.html
- WRC 2026 星海图展商页：https://www.worldrobotconference.com/expo/company/446.html
- WRC 2026 主论坛观点：https://www.worldrobotconference.com/news/3609.html
- 优必选 WRC 深度报道（量子位）：https://www.qbitai.com/2026/08/477253.html
- WRC 2026 具身智能复盘（36Kr/KrASIA）：https://eu.36kr.com/en/p/3953394550537606
- WRC 2026 实景作业复盘（KrASIA/36Kr）：https://kr-asia.com/at-world-robot-conference-2026-robots-face-the-test-of-real-work
- 银河通用 LDA-1B 论文：https://arxiv.org/abs/2602.12215
- 银河通用 Humanoid-GPT/AstraBrain-WBC 0.5 GitHub：https://github.com/GalaxyGeneralRobotics/Humanoid-GPT
- 银河通用 WBC 报道（量子位）：https://www.qbitai.com/2026/06/436813.html
- 智元 AGIBOT WORLD 2026 Theme 2：https://agibot.com/article/231/detail/72.html
- 智元 GO-1 报道（上观新闻）：https://www.shobserver.cn/wx/detail.do?id=873140
- 星海图 G0.5/Fast-WAM 报道（21 经济）：https://www.21jingji.com/article/20260617/herald/9e36e33f25a99595a8e3e3e6ff5e6f2d.html
- 自变量机器人官网新闻页：https://x2robot.com/news
- 影身智能 WRC 报道（中国日报）：https://cn.chinadaily.com.cn/a/202608/25/WS6a8d03aee4b09dff9814edff.html
- WRC 大模型赋能报道（腾讯云开发者社区）：https://cloud.tencent.com/developer/article/2731996?policyId=1003
- Unitree G1 官方页：https://www.unitree.com/g1
- Unitree/WRC 预热页：https://www.worldrobotconference.com/news/latestnews/2997.html
- NVIDIA Jetson Thor/T3000/T2000/Cosmos 3 Edge：https://blogs.nvidia.cn/blog/jetson-thor-robotics-edge-ai-agent/
- 黑芝麻智能 WRC 算力矩阵报道：https://wap.eastmoney.com/a/202608223850183143.html
