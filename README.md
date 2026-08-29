# WRC2026 具身智能 System 0/1/2 架构与参展厂商模型洞察

一页 PPT：[`WRC_WAIC_2026_embodied_system012_onepage.pptx`](./WRC_WAIC_2026_embodied_system012_onepage.pptx)

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
| 蚂蚁灵波 Robbyant | WRC 官方展商页披露展位 C111、“一脑多机”，同一 LingBot 跨本体具身基座模型驱动乐聚、R2、钛虎机器人做物流、药房、工业上下料。 | 未披露独立小脑名称；LingBot-VLA 2.0 统一 55 维动作空间，覆盖手臂、末端、夹爪、灵巧手、腰部、头部、移动底盘信号。 | LingBot-VLA 2.0，Sparse MoE Action Expert；LingBot-VA 2.0 为 Video-Action 世界动作模型。 | LingBot-World 2.0 实时交互世界模型；LingBot-Vision 1.0 视觉基础模型；LingBot-Depth 2.0 空间感知模型。 | WRC 展品显示 LingBot 跨本体部署到乐聚、R2、钛虎等异构机器人；GitHub 披露真实机器人部署入口；Depth 2.0 与奥比中光 Gemini 330 系列联合优化并提供 edge-side SDK。 | LingBot-VLA 2.0：6B；LingBot-VA：约 5B DiT；LingBot-Vision：1B；World 2.0 参数未披露。 | LingBot-VLA 2.0 GitHub 披露 RTX 4090D 上 10 步 denoising 单次推理约 130ms；LingBot-VA 推理需约 18-24GB VRAM。 | WRC 药房流程 60 秒内完成；物流 60 秒内数百件柔性包裹；VLA 单次推理约 130ms；World 2.0 标称 720p/60fps 实时交互、控制延迟低于 1 秒。 | WRC 官方: https://www.worldrobotconference.com/expo/company/765.html；物流: https://www.worldrobotconference.com/expo/product/1532.html；药房: https://www.worldrobotconference.com/expo/product/1535.html；工业: https://www.worldrobotconference.com/expo/product/1536.html；VLA: https://technology.robbyant.com/lingbot-vla-v2；HF: https://huggingface.co/robbyant/lingbot-vla-v2-6b；GitHub: https://github.com/robbyant/lingbot-vla-v2；VA: https://huggingface.co/docs/lerobot/en/lingbot_va；World: https://technology.robbyant.com/lingbot-world-v2 |
| 智元机器人 AGIBOT | 2026 年持续披露 AGIBOT WORLD 2026 数据集；WAIC 展示 GO-2、GE-2、Genie Evolver 1.0，发布 A3 Ultra/G2 Max 等新品。 | 产品体系称“三智合一”：运动智能、交互智能、操作智能；独立 System 0 模型参数未披露。 | GO-1 基于 Vision-Language-Latent-Action（ViLLA）；WAIC 披露 GO-2 采用动作思维链与异步双系统，打通规划与执行。 | GE-2 世界模型；Genie Evolver 1.0 闭环真机强化学习系统；AGIBOT WORLD 2026 Theme 2 面向世界模型、神经仿真、物理感知数据。 | GO-1/GO-2 支持多本体部署；WAIC 披露 A3 Ultra 增加 700 TOPS 具身处理器，G2 Max 支持 7x24 小时无人化码垛搬运。 | GO-1/GO-2/GE-2 参数未公开。 | A3 Ultra：700 TOPS 端侧具身处理器；G2 Max 双臂标准负载 38kg、峰值 50kg；行业二手口径称 GO-1/GO-2 百亿参数以内模型可在边缘端 10Hz-20Hz 推理，应视为二手口径。 | 官方模型 Hz 未披露；二手报道为 10Hz-20Hz 端侧推理。 | AGIBOT Theme 2: https://agibot.com/article/231/detail/72.html；上观 GO-1: https://www.shobserver.cn/wx/detail.do?id=873140；WAIC: https://finance.sina.com.cn/tob/2026-07-18/doc-iniierwe7241266.shtml；AGIBOT World: https://agibot-world.com/ |
| 智平方 AI2 Robotics | WRC 官方展商页披露 AlphaBrain 和 AlphaBot 2；财联社披露 GOVLA/FiS-VLA 路线。 | 与全身动作/控制深度结合，但独立小脑模型名称和频率未公开。 | GOVLA 系列，2025 年推出 GOVLA 0.5（FiS-VLA），公开口径为“异构输入+异步频率”双系统 VLA。 | AlphaBrain 作为全栈自研具身大模型核心；强调原生面向真实物理世界。 | AlphaBot 2 由 AlphaBrain 定义形态并驱动；具体端侧/云端部署未披露。 | 未披露。 | 未披露。 | “异步频率”披露为架构特征，具体 Hz 未披露。 | WRC 官方: https://www.worldrobotconference.com/expo/company/409.html；财联社: https://www.cls.cn/detail/2341170 |
| 星海图 Galaxea | WRC 官方展商页展示 Kengo、R1 Pro/Lite；WRC 期间发布 Nexo、Kengo、Lemo 三大整机；2026-06 发布 G0.5、Fast-WAM、全身控制基础模型。 | 高性能运动小脑、全身控制基础模型；Kengo 以运动小脑和具身大脑为核心；Nexo 强调力触与精密作业。 | G0.5 VLA 基础模型，面向作业智能；官方称已开源；Nexo 面向前置仓/物流长时序作业。 | Fast-WAM 世界模型；三层智能观：本能智能、作业智能、进化智能；中国日报披露 Fast-WAM 单步推理延迟约 190ms，相比传统范式约 800ms 提速 4 倍以上。 | R1 Lite 标配高算力计算平台，原生支持端侧模型部署；Nexo 会场介绍口径为约 700 TOPS 端侧算力，接近 NVIDIA Jetson/Thor T3000 865 FP4 TFLOPS 量级；该 700 TOPS 暂未检索到公开网页原文。 | 未披露。 | 会场口径：Nexo 约 700 TOPS；NVIDIA Thor/T3000 官方公开规格为 865 FP4 TFLOPS、32GB LPDDR5X。 | Fast-WAM：约 190ms 单步推理延迟；Nexo 作业控制频率未披露。 | WRC 官方: https://www.worldrobotconference.com/expo/company/446.html；21 经济: https://www.21jingji.com/article/20260617/herald/9e36e33f25a99595a8e3e3e6ff5e6f2d.html；中国日报: https://cn.chinadaily.com.cn/a/202608/24/WS6a8bec8e45ce1aabf7a935af.html；NVIDIA: https://blogs.nvidia.cn/blog/jetson-thor-robotics-edge-ai-agent/；700 TOPS 为用户提供的 WRC 会场介绍口径，待补公开链接。 |
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

1. 大多数厂商未披露模型参数、显存、芯片型号和推理 Hz，尤其是智平方 AlphaBrain/GOVLA、自变量 WALL-B、傅利叶 GR-3 框架；星海图虽有 Nexo 约 700 TOPS 会场口径和 Fast-WAM 190ms 公开报道，但 G0.5/Fast-WAM 参数仍未披露。
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
- 蚂蚁灵波 WRC 展商页：https://www.worldrobotconference.com/expo/company/765.html
- 蚂蚁灵波 WRC 物流/药房/工业展品页：https://www.worldrobotconference.com/expo/product/1532.html；https://www.worldrobotconference.com/expo/product/1535.html；https://www.worldrobotconference.com/expo/product/1536.html
- 蚂蚁灵波 LingBot-VLA 2.0 官方技术页/GitHub/HuggingFace：https://technology.robbyant.com/lingbot-vla-v2；https://github.com/robbyant/lingbot-vla-v2；https://huggingface.co/robbyant/lingbot-vla-v2-6b
- 蚂蚁灵波 LingBot-World 2.0 / LingBot-VA：https://technology.robbyant.com/lingbot-world-v2；https://huggingface.co/docs/lerobot/en/lingbot_va
- 智元 AGIBOT WORLD 2026 Theme 2：https://agibot.com/article/231/detail/72.html
- 智元 GO-1 报道（上观新闻）：https://www.shobserver.cn/wx/detail.do?id=873140
- 星海图 G0.5/Fast-WAM 报道（21 经济）：https://www.21jingji.com/article/20260617/herald/9e36e33f25a99595a8e3e3e6ff5e6f2d.html
- 星海图 Nexo/G0.5/Fast-WAM 技术论坛报道（中国日报）：https://cn.chinadaily.com.cn/a/202608/24/WS6a8bec8e45ce1aabf7a935af.html
- 自变量机器人官网新闻页：https://x2robot.com/news
- 影身智能 WRC 报道（中国日报）：https://cn.chinadaily.com.cn/a/202608/25/WS6a8d03aee4b09dff9814edff.html
- WRC 大模型赋能报道（腾讯云开发者社区）：https://cloud.tencent.com/developer/article/2731996?policyId=1003
- Unitree G1 官方页：https://www.unitree.com/g1
- Unitree/WRC 预热页：https://www.worldrobotconference.com/news/latestnews/2997.html
- NVIDIA Jetson Thor/T3000/T2000/Cosmos 3 Edge：https://blogs.nvidia.cn/blog/jetson-thor-robotics-edge-ai-agent/
- 黑芝麻智能 WRC 算力矩阵报道：https://wap.eastmoney.com/a/202608223850183143.html
- WAIC 2026 官方展商入口：https://www.worldaic.com.cn/exhibitors
- 蚂蚁灵波 WAIC 2026 智慧药房/全栈大脑 2.0：https://app.xinhuanet.com/news/article.html?articleId=202607171b0f027e70f342f7ad1a912a51136a9c；https://www.antgroup.com/hk/news-media/press-releases/1784275200000
- 奥比中光 x 蚂蚁灵波 LingBot-Depth 2.0：https://www.orbbec.com.cn/index/News/info.html?cate=31&id=377
- 智元 WAIC 2026 A3 Ultra/GO-2/GE-2：https://finance.sina.com.cn/tob/2026-07-18/doc-iniierwe7241266.shtml；https://finance.sina.com.cn/roll/2026-07-18/doc-iniieweh1526205.shtml
- 地瓜机器人 RDK S600 官方规格/WAIC 量产验证：https://developer.d-robotics.cc/rdks600；https://www.stdaily.com/web/gdxw/2026-07/16/content_548005.html；https://www.shobserver.cn/wx/detail.do?id=1144380
- MiniCPM-Robot 官方 GitHub：https://github.com/OpenBMB/MiniCPM-Robot
- 黑芝麻智能 WAIC 2026 SesameX：https://www.blacksesame.com/zh/list_8/994.html
- 魔法原子 Magic-VLA K02 WAIC：https://autonews.gasgoo.com/articles/news/waic-2026-magiclab-launches-three-new-robot-products-embodied-intelligence-accelerates-entry-into-factories-and-public-scenarios-2079099620004175873；https://finance.sina.com.cn/tech/roll/2026-07-19/doc-iniihqpn1240008.shtml
- 普渡 WAIC 2026 PuduFM/PUDU D7：https://finance.sina.com.cn/roll/2026-07-17/doc-iniicqkw1429495.shtml；https://www.prnewswire.com/news-releases/pudu-embodied-unveils-the-next-generation-pudu-d7-opening-a-new-chapter-for-industrial-semi-humanoid-robotics-302786976.html
- 北京人形 WRC 2026 Pelican-Unify/天工 Omni：https://www.x-humanoid.com/news-view-292.html；https://www.beijing.gov.cn/fuwu/lqfw/gggs/202608/t20260821_4831885.html
- 北京人形慧思开物/PelicanVLM/Wow/XR-1：https://www.x-humanoid.com/detail/hskw.html
- 中国兵器/杭州智元研究院 WRC 2026：https://finance.sina.com.cn/enterprise/central/2026-08-20/doc-ininxzpp6777485.shtml；https://cn.chinadaily.com.cn/a/202608/20/WS6a86c3f0a3105d3d7a27c0f7.html
- 灵境智源 N1100/N1000 具身智脑：https://www.mscapetech.com/index/products；https://mscapetech.com/index/yingyong/detail/id/10004
- 科大讯飞 x 灵境智源合作：https://www.guandian.cn/m/show/522426
- NVIDIA Jetson Thor/T5000/T4000 官方规格：https://www.nvidia.cn/autonomous-machines/embedded-systems/jetson-thor/

## 8. 一页 PPT 版：按部署方式分类的大脑模型表

### 8.1 机器人“大脑”与端侧/端云部署

| 部署方式 | 厂商/项目 | 大脑模型 | 模型架构 | 参数/数据 | 算力/部署 | 推理频率/延迟 |
|---|---|---|---|---|---|---|
| 纯端侧/本体端侧 | 蚂蚁灵波 Robbyant | LingBot-VLA 2.0；LingBot-VA 2.0；LingBot-World/Vision/Depth | VLA + Sparse MoE Action Expert；Video-Action；世界模型/视觉/深度模型 | VLA 6B；VA 约 5B DiT；Vision 1B；约 6 万小时、20+ 本体；Depth 1.5 亿数据 | VLA 在 RTX 4090D 本地推理；VA 约 18-24GB VRAM；Depth 提供 edge-side SDK | VLA 约 130ms；World 720p/60fps、控制延迟 <1s |
| 纯端侧/本体端侧 | MiniCPM-Robot | RobotManip；RobotTrack | 操作 VLA；目标跟踪 VLA | 1.5B；0.9B；60 帧上下文推理量 125→3.3 TFLOPs/决策步 | H100/H20；RobotTrack 部署 Jetson Orin NX 16GB | H100 120ms；H20 10→37Hz；Go2 本地 5+FPS/180ms |
| 纯端侧/本体端侧 | 地瓜机器人 / 它石智航 | S600 适配 3B 具身大模型 | 端侧具身模型适配与算力平台 | 3B；首期 5 天真机部署 | 560 TOPS INT8；32/64GB LPDDR5；204.8GB/s | 端侧稳定运行，Hz 未公开 |
| 纯端侧/本体端侧 | 蔚蓝智能 BabyAlpha | BabyAlpha A3 本地交互大模型 | 本地 LLM / 环境识别 / 对话 | 7B | 本地端侧运行 |  |
| 纯端侧/本体端侧 | 科大讯飞 / 灵境智源 | 讯飞机器人大脑；MScape N1100/MS-N1100 | 端侧具身智脑设备，具体模型未公开 |  | N1100 官网：1000 TOPS；14 路传感器融合 + 4 路 EtherCAT；用户会场口径：可按网络环境切换“云+端/纯端侧”，核心为 Jetson T5000、可选 T4000；T5000 官方 AI 性能 2070 FP4 TFLOPS、128GB、40-130W |  |
| 纯端侧/弱网备用 | 中国兵器 / 杭州智元研究院 | 具身大脑通用套件 | 多模态大模型驱动的“感知-记忆-规划-执行-反思”闭环 |  | 公开稿披露具身大脑通用套件、机动抗毁边缘云；用户会场观察：端侧大脑设备弱于云侧但无网可用 |  |
| 纯端侧/边缘部署 | 星海图 Galaxea | G0.5；Fast-WAM / GigaWorld-Policy-0.5；Nexo | 自回归 VLA；世界动作模型；长时序作业智能 | G0.5 基于 Qwen3.5 2B 初始化；18 本体；约 1 亿 VQA；预训练约 12 万步 | R1 Lite 原生支持端侧模型部署；Nexo 700 TOPS 为 WRC 会场口径，待公开链接 | Fast-WAM 约 190ms；A100 189ms、RTX 4090 110ms、C++ 85ms |
| 端云协同 | 优必选 UBTECH | Thinker；Thinker-VLA；Thinker-WM | 基座模型；VLA；世界模型 | Thinker 最大 100B | 高实时能力端侧，云端做慢推理、多机调度和模型管理；端侧显存 64GB→32GB | VLA 推理效率 +176% |
| 端云协同 | 智元机器人 AGIBOT | GO-1/GO-2；GE-2；Genie Evolver 1.0 | 具身基座模型；世界模型；闭环真机强化学习 |  | A3 Ultra：700 TOPS 端侧具身处理器；G2 Max 支持 7x24h 无人化码垛搬运 | 二手口径 10-20Hz；官方 Hz 未公开 |
| 端云协同 | 北京人形 / 天工 | Pelican-Unify；Pelican-VL 2.0；Wow/天悟；PelicanVLM；XR-1 | 统一表征具身世界模型；VLM；WFM；跨本体 VLA | PelicanVLM 72B；Pelican-Unify 百万小时数据池；Wow/WU 曾披露 5000+ 小时视频数据 | Pelican-Unify 已在天轶真机部署跑通；天工 Omni 端侧原生智能架构，支持大模型边缘部署 |  |
| 端云协同 | 机器科学 RoboScience | 具身世界模型 + 通用操作模型 | 世界模型；通用操作模型 |  | REX G1 与部署于腾讯云的模型配合 |  |
| 端云协同 | 傅利叶 Fourier | LLM + embodied models + planning | LLM；具身模型；导航规划 |  | 框架连接 LLM、具身模型、导航规划与低级运控 |  |
| 云侧/平台/未明 | 银河通用 Galbot | LDA-1B；AstraBrain WAM；Grasp/Grocery/TrackVLA | 隐式世界-动作模型；WAM；VLA | LDA-1B：1B | 跨本体后训练；生产部署算力未公开 |  |
| 云侧/平台/未明 | 智平方 AI2 Robotics | AlphaBrain；GOVLA / FiS-VLA | 具身大模型；双系统 VLA；异构输入 + 异步频率 |  |  |  |
| 云侧/平台/未明 | 自变量 X Square | WALL-B；Wall-OSS-0.5 | 世界统一模型；视觉/语言/动作统一；端到端模型 | Wall-OSS-0.5 真机测试 17 个任务 | 模型上真机，端云分工未公开 |  |
| 云侧/平台/未明 | 普渡 Pudu | PuduFM 1.0；Pudu Agent OS | Physical Agent；具身基座模型；一脑多形 | 每年 5000 万小时实景运行数据回流 | D7 自主换电；模型端侧算力未公开 |  |

### 8.2 算力单元参考

| 算力单元/平台 | 明确数据 | 对应部署方式 | PPT 用法 |
|---|---|---|---|
| NVIDIA Jetson T5000 / Jetson AGX Thor | 2070 FP4 TFLOPS（稀疏）；128GB LPDDR5X；273GB/s；40-130W。 | 纯端侧/端云协同的大脑推理、VLA、VLM、世界模型。 | 说明端侧机器人“大脑”已进入 2 PFLOPS FP4、128GB 内存档位。 |
| NVIDIA Jetson T4000 | 1200 FP4 TFLOPS（稀疏）；64GB LPDDR5X；273GB/s；40-70W。 | 中高阶本体端侧推理。 | 可解释 MS-N1100/N1100 等设备的可选配置和功耗档。 |
| 灵境智源 MScape N1100 | 1000 TOPS；14 路传感器融合；4 路 EtherCAT；万兆网口；Xenomai 硬实时。 | 纯端侧具身智脑设备。 | 可作为“模型未披露，但端侧智脑硬件量化明确”的样本。 |
| 地瓜机器人 RDK S600 | 560 TOPS INT8；32/64GB LPDDR5；204.8GB/s；适配 3B 具身模型。 | 国产端侧大脑/边缘算力平台。 | 说明 3B 级模型已经可在国产端侧平台完成真机适配。 |
| 智元 A3 Ultra / 星海图 Nexo | A3 Ultra 公开口径 700 TOPS；Nexo 700 TOPS 为会场口径，待公开链接。 | 高阶人形本体端侧算力。 | 说明 700 TOPS 已成为 2026 高阶人形端侧算力锚点。 |

### 8.3 PPT 结论

1. 端侧算力正在从 Orin 级进入 700-1000 TOPS 与 1200-2070 FP4 TFLOPS 档：智元 A3 Ultra 700 TOPS、星海图 Nexo 700 TOPS 会场口径、灵境 N1100 1000 TOPS、Jetson T5000 2070 FP4 TFLOPS，说明高阶机器人开始把 VLA/VLM/世界模型下沉到本体或近端边缘设备。
2. 端侧模型能力已经不只做语音/识别：MiniCPM-Robot 1.5B/0.9B 能在 H20 从 10Hz 提到 37Hz、Go2 本地 5+FPS；蚂蚁灵波 6B VLA 在 RTX 4090D 约 130ms，World 2.0 标称 720p/60fps、控制延迟 <1s。端侧化趋势的核心不是完全替代云端，而是在断网、弱网、低时延和隐私场景保证基本自主。
3. 端云协同仍是更强模型能力的主线：优必选把高实时能力放端侧、云端承担慢推理和多机调度；北京人形 Pelican-Unify 用 VLM/action model/WFM 打通理解、预演、执行，并以百万小时数据池迭代；机器科学 REX G1 直接配合腾讯云世界模型和通用操作模型。云侧优势在于更大模型、更强规划、更快迭代，端侧优势在于低延迟、可离线和数据安全。

### 8.4 官方来源补充清单

| 厂商 | WRC 官方展位/展品信息 | 公司/项目官方硬数据 | PPT 可引用点 |
|---|---|---|---|
| 蚂蚁灵波 Robbyant | 展位 C111；WRC 官方写明“基于蚂蚁灵波跨本体具身大模型”的物流、药房、工业三套展示方案。 | LingBot-VLA 2.0：6B、约 6 万小时预训练数据、20 种机器人构型、55 维动作空间、RTX 4090D 上约 130ms；LingBot-VA 约 5B DiT、18-24GB VRAM。 | “一脑多机”最适合做 PPT 例子：同一大脑驱动不同本体和不同场景。 |
| 银河通用 Galbot | 展位 C104；WRC 官方披露 AstraBrain WAM/WBC、GraspVLA/GroceryVLA/TrackVLA。 | LDA-1B：1B；AstraBrain-WBC 0.5：8040 万参数、RTX 4090 <1.5ms、50Hz 闭环、Jetson Orin onboard 部署。 | “大小脑硬指标最完整”：WAM/VLA + WBC 小脑 + 频率/算力。 |
| 优必选 UBTECH | 展位 C103；Walker S2 支持 3 分钟热插拔换电、7×24 小时运行；Walker C1 搭载大模型驱动多模态内核。 | Thinker 最大 100B；VLA 端侧显存从 64GB 优化到 32GB；推理效率提升 176%。 | “端侧工程化”：大模型能力压到机器人本体。 |
| 星海图 Galaxea | 展位 C210；Kengo 以高性能运动小脑与具身大脑为核心，R1 Lite 原生支持端侧模型部署。 | Fast-WAM 公开报道单步约 190ms；G0.5 复现计划开放权重/接口/评测/微调工具；Nexo 会场口径约 700 TOPS。 | “端侧算力+世界模型延迟”：Nexo 700TOPS、Fast-WAM 190ms。 |
| 自变量 X Square | 展位 C107；量子 1 Pro/量子 2 号搭载端到端具身智能基座大模型，量子 2 号最多 62 自由度。 | Wall-OSS-0.5 上真机测试 17 个任务；WALL-B 世界统一模型；参数/算力未公开。 | “统一模型路线”：视觉、语言、动作从分段走向统一。 |
| 宇树 Unitree | 展位 C312；G1、B2、Go2、H2，WRC 官方披露 G1 23-43 关节、H2 超高算力平台。 | G1 官方基础算力 8 核 CPU；高算力模块可选 Orin；G1-D 官方写 Orin NX 16GB 100TOPS 等可选项。 | “本体平台”：适合作为第三方 VLA/小脑部署载体。 |
| 傅利叶 Fourier | 展位 C229；GR-3 为 Care-bot，柔肤软包覆与全感交互系统。 | WRC 复盘报道称其框架连接 LLM、具身模型、导航规划和低级运控；参数/算力未披露。 | “通用控制栈”：把模型和本体适配层平台化。 |
| 魔法原子 MagicLab | 展位 C323；WRC 官方披露“通用大脑+模块化全栈产品”架构和原子万象大模型。 | 参数/算力未披露；官方披露 MagicBot 42 自由度、已进驻追觅工厂实训。 | 可作为“物理 AI 原生平台”补充案例，但硬数据不足。 |
| 北京人形 / 天工 | WRC 发布 Pelican-Unify 与天工 Omni；官方称 Pelican-Unify 集成 VLM、action model、WFM，并在天轶真机部署跑通。 | PelicanVLM 72B；Pelican-Unify 百万小时数据池；天工 Omni 端侧原生智能架构，支持大模型边缘部署。 | “统一表征世界模型 + 端侧原生本体”代表国家队路线。 |
| 中国兵器 / 杭州智元研究院 | 中国兵器牵头央企展区；公开稿披露“具身大脑通用套件”和“机动抗毁边缘云”。 | 具身大脑通用套件基于多模态大模型构建“感知-记忆-规划-执行-反思”闭环；模型参数未公开。 | 可作为弱网/无网端侧大脑设备样本，用户会场观察为“无网可用，智能弱于云侧”。 |
| 科大讯飞 / 灵境智源 | 灵境智源与科大讯飞 2025 年达成具身智能战略合作；灵境 N1100 为第三代人形机器人大脑。 | N1100 官方披露 1000 TOPS、14 路传感器融合、4 路 EtherCAT、Xenomai；用户会场观察为讯飞机器人大脑 MS-N1100 可按网络切换云+端/纯端侧，核心 Jetson T5000/可选 T4000。 | 端侧智脑硬件数据明确，但具体大脑模型名称未披露。 |

## 9. WAIC 2026 增量：WRC 前置验证与硬数据补充

WAIC 2026 于 2026 年 7 月 17-20 日在上海举行，时间上早于 WRC 2026 约一个月。官网可确认展商入口，但展商详情页动态加载较多；本轮主要采用 WAIC 期间公司官方稿、官方 GitHub/技术页和新华社、上证报、科技日报等可追溯材料。结论是：WAIC 的增量不只是“又一场展会”，而是提前暴露了端侧部署、真实场景运营和国产算力适配三条主线。

### 9.1 有明确模型/算力/频率数据的 WAIC 增量表

| 厂商/项目 | WAIC 增量信息 | 模型类别与名称 | 参数/数据规模 | 算力/部署/频率 | 对 WRC 报告的影响 | 来源 |
|---|---|---|---|---|---|---|
| 蚂蚁灵波 Robbyant | WAIC “十大镇馆之宝”之一为“基于蚂蚁灵波跨本体具身大模型的机器人智慧药房”；H3-B302、H1-C701 展台展示乐聚、星尘、自研 R-2 三种本体按随机订单协同作业。 | LingBot-VLA 2.0；LingBot-Depth 2.0；“全栈大脑 2.0”。 | LingBot-VLA 2.0：6B，约 6 万小时真实数据，适配 17 家厂商 20 余种机器人构型；LingBot-Depth 2.0：基于 1.5 亿规模数据训练。 | VLA 2.0 已有 RTX 4090D 单次约 130ms 口径；Depth 与 Gemini 330/EGO RGB-D 打通采集和端侧感知，16 项评测 12 项 RMSE 最优，透明物体误差低至 0.010。 | 强化“蚂蚁灵波是公开硬数据最完整的大脑供应商之一”；PPT 中可从 WRC“一脑多机”升级为 WAIC/WRC 连续验证。 | https://app.xinhuanet.com/news/article.html?articleId=202607171b0f027e70f342f7ad1a912a51136a9c；https://www.orbbec.com.cn/index/News/info.html?cate=31&id=377；https://github.com/robbyant/lingbot-vla-v2 |
| 智元机器人 AGIBOT | WAIC 发布远征 A3 Ultra、精灵 G2 Max、灵犀 X2 EDU 等，并展示 GO-2、GE-2、Genie Evolver 1.0；60 台智元机器人参与大会公共服务。 | GO-2 具身基座大模型；GE-2 世界模型；Genie Evolver 1.0 闭环真机强化学习系统。 | GO-2/GE-2 参数未披露；A3 Ultra 为 174cm 商用全尺寸人形；G2 Max 双臂标准负载 38kg、峰值 50kg。 | A3 Ultra 增加 700 TOPS 具身处理器、激光雷达、多部位鱼眼相机；G2 Max 支持 7x24 小时无人化码垛搬运。 | 需更新智元行：其 WRC 前已披露 700 TOPS 端侧处理器和 GO-2/GE-2 名称，但模型参数仍空缺。 | https://finance.sina.com.cn/tob/2026-07-18/doc-iniierwe7241266.shtml；https://finance.sina.com.cn/roll/2026-07-18/doc-iniieweh1526205.shtml |
| 地瓜机器人 D-Robotics / 它石智航 A3 | WAIC 前发布旭日 S600 量产验证进展；它石智航 A3 轮式双臂工业机器人搭载 S600，面向工业、汽车、科研场景规模化落地。 | 端侧算力平台，不是 VLA 模型；适配 3B 参数级具身大模型。 | 3B 参数级具身大模型完成 S600 平台适配；S600 面向 Pi0/0.5、Qwen3-VL-8B、Whisper 等端侧模型。 | S600：560 TOPS @INT8，4 核 Nash BPU，18x A78AE CPU，6x R52+ MCU，32/64GB LPDDR5，204.8GB/s 带宽；它石 A3 首期 5 天真机部署，端侧稳定运行。 | 这是 WAIC 最清晰的“国产端侧算力+具身模型”案例，可作为 NVIDIA Thor/Orin 之外的国产算力参照。 | https://developer.d-robotics.cc/rdks600；https://www.stdaily.com/web/gdxw/2026-07/16/content_548005.html；https://www.shobserver.cn/wx/detail.do?id=1144380 |
| 面壁智能 / OpenBMB MiniCPM-Robot | WAIC 期间发布并开源 MiniCPM-Robot 系列，虽非整机厂，但给出非常完整的端侧具身模型数据。 | MiniCPM-RobotManip 通用操作 VLA；MiniCPM-RobotTrack 目标跟踪 VLA。 | Manip：1.5B；Track：0.9B。Manip 支持 1 分钟视觉上下文，60 帧历史流式推理从 125 TFLOPs/决策步降至 3.3 TFLOPs。 | H100 BF16 单帧前向 120ms；PhyAI 将 H20 推理从 10Hz 提至 37Hz；RobotTrack 在 Unitree Go2 EDU 本地视觉跟踪 5+ FPS、约 180ms，部署硬件为 Jetson Orin NX 16GB。 | 可补到“开源模型/第三方大脑”页：小模型端侧化比单纯参数堆叠更适合机器人。 | https://github.com/OpenBMB/MiniCPM-Robot |
| 黑芝麻智能 SesameX | WAIC 官方稿披露 SesameX 从车规芯片扩展到机器人，展出 Kalos、Aura 开发套件及搭载黑芝麻芯片的云深处绝影 X30。 | SesameX 全脑智能计算平台；Kalos/Aura/Liora 三档计算模组。 | 非模型参数；Kalos/Aura/Liora 覆盖 48TOPS 至近 600TOPS。 | WAIC 口径：48TOPS 至近 600TOPS；2025 发布页披露 Kalos 48TOPS、Aura 70TOPS、Liora 近 600TOPS；WRC 后续口径升至 Liora 700TOPS。 | 报告中黑芝麻应区分 WAIC 近 600TOPS 与 WRC 700TOPS 两个时间口径。 | https://www.blacksesame.com/zh/list_8/994.html；https://www.blacksesame.com/zh/list_8/914.html |
| 星海图 Galaxea | WAIC 未检索到比 WRC/开发者大会更多的 Nexo 算力公开网页；但 WAIC 前后官方/论文链条补强 G0.5 与 WAM 延迟数据。 | G0.5 自回归 VLA；Fast-WAM / GigaWorld-Policy-0.5 世界动作模型；全身控制基础模型。 | G0.5 基于 Qwen3.5 2B 初始化，18 种本体，约 1 亿条 VQA，预训练约 12 万步；模型总参数未披露。 | Fast-WAM 公开口径 190ms；GigaWorld-Policy-0.5 论文披露 A100 189ms、RTX 4090 110ms、C++ 部署 85ms。 | 可把星海图从“只有 Nexo 700TOPS 会场口径”补充为“模型延迟有论文硬指标，但 Nexo 700TOPS 仍待公开链接”。 | https://opengalaxea.github.io/G05/；https://arxiv.org/html/2607.13960v1；https://www.geekpark.net/news/365536 |
| 魔法原子 MagicLab | WAIC 发布 MagicBot X1/D1/T1 和 Magic-VLA K02，现场展示叠盒封胶、柔性衣物整理、行李箱收纳。 | Magic-VLA K02；此前上交会材料提到 Magic-Mix 世界模型。 | 参数未披露。 | 叠盒封胶组合式长程任务成功率超过 90%；X1 31 主动自由度、单关节峰值扭矩 450N·m；D1 已进驻追觅工厂常态验证。 | 适合补“长程任务成功率”案例，但因无参数/算力，不建议放一页 PPT 主表。 | https://autonews.gasgoo.com/articles/news/waic-2026-magiclab-launches-three-new-robot-products-embodied-intelligence-accelerates-entry-into-factories-and-public-scenarios-2079099620004175873；https://finance.sina.com.cn/tech/roll/2026-07-19/doc-iniihqpn1240008.shtml |
| 普渡机器人 Pudu | WAIC 首次线下展示 PUDU D7，并阐释“一脑多形”Physical Agent 全栈技术架构。 | PuduFM 1.0 具身智能基座模型；Pudu Agent OS。 | 模型参数未披露；累计出货超 13 万台、覆盖 85 个国家、5 万余家客户，每年超 5000 万小时实景运行数据回流。 | D7 最高负载 14kg、作业高度 2m、自主换电；公开 WAIC 材料未披露模型端侧算力。 | 可补“商用服务机器人数据飞轮”，但缺参数/算力，PPT 主表优先级低于蚂蚁/银河/智元/地瓜/MiniCPM。 | https://finance.sina.com.cn/roll/2026-07-17/doc-iniicqkw1429495.shtml；https://www.prnewswire.com/news-releases/pudu-embodied-unveils-the-next-generation-pudu-d7-opening-a-new-chapter-for-industrial-semi-humanoid-robotics-302786976.html |
| 千觉机器人 X-Touch | WAIC 展示 VTLA 触觉具身模型、XTac UMI G1 视触觉数据采集夹爪、触觉传感器矩阵。 | VTLA（视觉-触觉-语言-动作）模型；X-TouchMind V1；TacVerse 数据集。 | 参数未披露。 | 触觉传感器毫秒级响应；XTac UMI G1 同步采集视觉、触觉、位姿轨迹、夹爪状态等五维数据。 | 可补 System 0/1 的触觉闭环方向，但因参数算力不足，仅作趋势项。 | https://www3.xinhuanet.com/tech/20260720/6cbb5f1efd99431ab721ffce590e72f1/c.html；https://www.stcn.com/article/detail/4027332.html |

### 9.2 对一页 PPT 的更新建议

1. 如果只留 5 个“硬数据玩家/项目”：银河通用、蚂蚁灵波、智元、地瓜机器人/它石智航、MiniCPM-Robot。
2. 智元应从“参数算力未披露”调整为“模型参数未披露，但 A3 Ultra 端侧算力 700TOPS 已有 WAIC 媒体披露”。
3. 地瓜 S600 不是机器人整机厂，但它给了最清晰的国产端侧算力样本：560TOPS、32/64GB、高带宽、3B 具身模型 5 天真机部署。
4. MiniCPM-Robot 不是整机厂，但它是 WAIC 前后最适合说明“小参数、端侧、低延迟”的开源具身模型样本：1.5B/0.9B、H20 37Hz、Go2 本地 5+FPS。
5. 星海图 Nexo 700TOPS 仍应标注为“会场口径待补公开链接”；可用 G0.5/Fast-WAM/GigaWorld 的公开论文数据补足模型侧证据。
