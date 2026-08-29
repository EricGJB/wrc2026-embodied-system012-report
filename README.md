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
| 宇树 Unitree | WRC 期间王兴兴演讲指出泛化不足是瓶颈；G1/H1/R1 等本体和格斗赛热度高；G1-D 官方页披露模型训练与推理工具链。 | 公开产品强调模仿与强化学习驱动和毫秒级响应控制，但未披露具名小脑模型参数。 | 公司推进 VLA 路线；G1-D 支持自定义模型开发、部署与分布式训练。 | UnifoLM-WMA-0 是开源 World-Model-Action 架构；公开报道称 UnifoLM-X1-0 工业级具身大模型已在自有工厂试点部署；王兴兴称 VLA 和世界模型是主流路线。 | G1-D 支持本体端侧高算力模组和模型部署工具；工业模型试点为自有工厂场景。 | UnifoLM-WMA-0/UnifoLM-X1-0 参数未公开。 | G1-D 高算力模组可选 Orin NX 16GB（100 TOPS），基础算力为 8 核高性能 CPU。 | G1-D 官方披露系统遥操作延迟低于 100ms、采样率 60Hz；第三方 H1 论文的 50Hz 控制不能等同于宇树量产模型。 | Unitree G1-D: https://www.unitree.com/mobile/G1-D/；北京新闻: https://www.bjnews.com.cn/detail/1786102945129070.html；WRC 预热: https://www.worldrobotconference.com/news/latestnews/2997.html |
| 云深处 Deep Robotics | WAIC/黑芝麻材料展示搭载黑芝麻芯片的云深处绝影 X30；X30 官方页强调工业级四足巡检与全地形能力。 | 未公开具名小脑模型。 | 未检索到云深处具名 VLA 或技能模型。 | 未检索到云深处具名大脑模型。 | 可作为端侧算力生态样本，但 X30 官方页未披露具体芯片型号和模型部署方式。 | 未披露。 | 黑芝麻 Aura 平台为 70 TOPS，并被公开材料列为在云深处等伙伴产品中进入商用阶段；WAIC 官方稿称 X30 搭载黑芝麻芯片。 | 未披露。 | 黑芝麻 WAIC: https://www.blacksesame.com/zh/list_8/994.html；Aura 商用: https://caifuhao.eastmoney.com/news/20251121175709043148940；X30 官方: https://www.deeprobotics.cn/robot/wap/product3.html |
| 魔法原子 MagicLab | WAIC 发布 MagicBot X1/D1/T1 和 Magic-VLA K02；官方 MagicBot Gen1 页披露高性能计算模块。 | 官方披露 motion control network、6D visual servoing 和 full-body imitation learning，但未公开具名小脑模型参数。 | Magic-VLA K02 采用高层任务理解与低层动作执行双系统。 | Magic-Mix 是自研世界模型，包含 Magic-Mix WAM 和 Magic-Mix Creator。 | MagicBot Gen1 有端侧 AI 处理器；Magic-VLA K02/Magic-Mix 的生产部署位置未公开。 | Magic-VLA K02/Magic-Mix 参数未披露。 | MagicBot Gen1 官方规格为 8-core CPU + 100 TOPS AI Processor。 | 未披露。 | WAIC: https://autonews.gasgoo.com/articles/news/waic-2026-magiclab-launches-three-new-robot-products-embodied-intelligence-accelerates-entry-into-factories-and-public-scenarios-2079099620004175873；Gen1: https://www.magiclab.top/en/human；Magic-Mix: https://www.gasgoo.com/robot/news/70456157-%E9%AD%94%E6%B3%95%E5%8E%9F%E5%AD%90%E5%8F%91%E5%B8%83%E8%87%AA%E7%A0%94%E4%B8%96%E7%95%8C%E6%A8%A1%E5%9E%8Bmagic-mix/ |
| 京东 JoyAI | WRC 官方材料披露京东“机器人+AI 生态”；京东近期发布 JoyAI 模型矩阵。 | 未公开具名小脑模型。 | JoyAI-RA 0.1 是面向机器人自治的 VLA 基础模型。 | JoyAI-RA 0.5 是 Vision-Language-World-Action 框架，包含 VLM、latent-action-conditioned world model 和 flow-matching action expert；JoyAI 矩阵覆盖语音、图像、视频、实时交互、世界模型和具身智能等 7 个基础模型方向。 | 京东云/JoyInside 提供生态与云侧能力，但机器人本体端侧部署未公开。 | JoyAI-RA 0.1/0.5 参数未披露。 | 未披露机器人本体端侧算力。 | 未披露。 | WRC 官方: https://www.worldrobotconference.com/news/3600.html；JoyAI 报道: https://finance.people.com.cn/BIG5/n1/2026/0720/c1004-40764320.html；JoyAI-RA 0.5: https://arxiv.org/abs/2608.05674；JoyAI-RA 0.1: https://arxiv.org/abs/2604.20100 |
| 星尘智能 Astribot | WRC 官方展位页披露自研 Lumo 基座模型和 DuoCore 商业化部署框架；WAIC 期间星尘产品与 TurboX IRB10、RDK S100 等算力平台完成落地或适配。 | DuoCore-FS 的 fast pathway 负责高频动作生成，但未作为独立小脑模型披露参数。 | DuoCore-FS 是异步 Fast-Slow VLA，采用快路径高频动作生成和慢路径 VLM 推理。 | Lumo 是自研基座模型；DuoCore 是商业化部署框架，平衡复杂任务理解与实时动作控制。 | WRC 官方表述指向商业化部署框架；TurboX IRB10 与 RDK S100 资料均指向本体端侧推理或适配。 | DuoCore-FS 支持 3B 参数 VLM；Lumo 参数未公开。 | TurboX IRB10 基于 Qualcomm Dragonwing IQ10，最高 700 TOPS；星尘 T1 已适配 RDK S100，S100 BPU 为 80 TOPS。 | DuoCore-FS 达到 30Hz 全身 action-chunk generation。 | WRC 官方: https://www.worldrobotconference.com/expo/company/441.html；DuoCore-FS: https://arxiv.org/abs/2512.20188；TurboX: https://www.thundersoft.com/waic-2026/news/turbx-irb10-stardust-robot-waic/；S100: https://www.zidonghua.com.cn/news/program/293135.html |

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
- 机器科学 RoboScience REX G1/Visics：https://app.xinhuanet.com/news/article.html?articleId=202608203015cd806bf8432889dd72e71d8f9804；https://new.qq.com/rain/a/20260820A03U3C00
- 宇树 UnifoLM/G1-D：https://www.unitree.com/mobile/G1-D/；https://www.bjnews.com.cn/detail/1786102945129070.html
- 云深处/黑芝麻 Aura：https://www.blacksesame.com/zh/list_8/994.html；https://caifuhao.eastmoney.com/news/20251121175709043148940；https://www.deeprobotics.cn/robot/wap/product3.html
- 魔法原子 MagicLab 官方/Magic-Mix：https://www.magiclab.top/en/human；https://www.gasgoo.com/robot/news/70456157-%E9%AD%94%E6%B3%95%E5%8E%9F%E5%AD%90%E5%8F%91%E5%B8%83%E8%87%AA%E7%A0%94%E4%B8%96%E7%95%8C%E6%A8%A1%E5%9E%8Bmagic-mix/
- 京东 JoyAI/JoyAI-RA：https://www.worldrobotconference.com/news/3600.html；https://finance.people.com.cn/BIG5/n1/2026/0720/c1004-40764320.html；https://arxiv.org/abs/2608.05674；https://arxiv.org/abs/2604.20100
- 星尘智能 Astribot Lumo/DuoCore/TurboX/S100：https://www.worldrobotconference.com/expo/company/441.html；https://arxiv.org/abs/2512.20188；https://www.thundersoft.com/waic-2026/news/turbx-irb10-stardust-robot-waic/；https://www.zidonghua.com.cn/news/program/293135.html

## 8. 一页 PPT 版：按部署方式分类的大脑模型表

### 8.1 机器人“大脑”与端侧/端云部署

下表为一页 PPT 的代表样本，不等同于全部统计名单；部署方式占比按第 10 节的 25 家/项目扩展样本计算。本轮检索显示：宇树、魔法原子、京东和星尘智能已有具名大脑模型或架构公开；宇树、云深处、魔法原子和星尘智能有端侧算力或推理频率量化信息；京东公开了 JoyAI-RA 机器人模型框架，但未检索到机器人本体算力或参数量。

| 部署方式 | 厂商/项目 | 大脑模型、架构和参数 | 小脑模型 | 算力/部署 | 推理频率/延迟 |
|---|---|---|---|---|---|
| 纯端侧/本体端侧：模型或智脑设备主要在机器人本体运行，优点是离线可用、低时延和数据不出端，缺点是受功耗、显存和散热约束；扩展样本中 8/25 家，占 32.0%。 | 蚂蚁灵波 Robbyant | LingBot-VLA 2.0 是 6B VLA，采用 Sparse MoE Action Expert；LingBot-VA 2.0 是约 5B DiT 世界动作模型，LingBot-Vision 为 1B，LingBot-World 参数未公开。 |  | LingBot-VLA 2.0 在 RTX 4090D 本地推理；LingBot-VA 需要约 18-24GB VRAM；LingBot-Depth 提供 edge-side SDK。 | LingBot-VLA 2.0 单次推理约 130ms；LingBot-World 2.0 标称 720p/60fps，控制延迟低于 1s。 |
|  | MiniCPM-Robot | MiniCPM-RobotManip 是 1.5B 通用操作 VLA，MiniCPM-RobotTrack 是 0.9B 目标跟踪 VLA。 |  | RobotManip 可在 H100/H20 推理，RobotTrack 可部署在 Jetson Orin NX 16GB。 | H100 BF16 单帧前向 120ms；H20 推理从 10Hz 提升至 37Hz；Go2 本地跟踪 5+FPS、约 180ms。 |
|  | 地瓜机器人 / 它石智航 | RDK S600 已适配 3B 参数级具身大模型，模型名称未公开。 |  | RDK S600 提供 560 TOPS INT8、32/64GB LPDDR5 和 204.8GB/s 内存带宽。 |  |
|  | 星海图 Galaxea | G0.5 是自回归 VLA，基于 Qwen3.5 2B 初始化；Fast-WAM/GigaWorld-Policy-0.5 是世界动作模型，模型总参数未公开。 | 星海图披露高性能运动小脑和全身控制基础模型，但未公开参数。 | R1 Lite 原生支持端侧模型部署；Nexo 700 TOPS 为 WRC 会场口径，待公开链接。 | Fast-WAM 单步约 190ms；GigaWorld-Policy-0.5 在 A100 为 189ms、RTX 4090 为 110ms、C++ 部署为 85ms。 |
|  | 蔚蓝智能 BabyAlpha | BabyAlpha A3 本地运行 7B 交互大模型，用于对话和环境识别。 |  | BabyAlpha A3 本地端侧运行，芯片型号未公开。 |  |
|  | 宇树 Unitree | UnifoLM-WMA-0 是开源 World-Model-Action 架构，UnifoLM-X1-0 是工业级具身大模型，公开报道称已在自有工厂试点部署，公司同时推进 WMA 与 VLA 路线。 | 宇树公开产品以运动控制见长，但未公开具名小脑模型参数。 | G1-D 高算力模组可选 Orin NX 16GB（100 TOPS），基础算力为 8 核高性能 CPU。 | G1-D 系统遥操作延迟低于 100ms，采样率为 60Hz。 |
|  | 云深处 Deep Robotics | 未检索到具名大脑模型；WAIC/黑芝麻生态材料显示绝影 X30 搭载黑芝麻芯片。 | 未公开具名小脑模型。 | 黑芝麻 Aura 平台为 70 TOPS，并被公开材料列为在云深处等伙伴产品进入商用阶段；X30 官方产品页未披露芯片型号。 |  |
|  | 星尘智能 Astribot | Lumo 是自研基座模型，DuoCore 是商业化部署框架；DuoCore-FS 是快慢异步全身 VLA，支持 3B VLM。 | DuoCore-FS 的 fast pathway 负责高频动作生成，但未作为独立小脑模型披露参数。 | TurboX IRB10 在星尘产品上首个落地，最高 700 TOPS；星尘 T1 已适配 RDK S100，其 BPU 为 80 TOPS。 | DuoCore-FS 达到 30Hz whole-body action-chunk generation。 |
| 端云协同/可切换：端侧处理高实时或离线兜底，云侧处理慢推理和多机调度，优点是兼顾低延迟和更强模型，缺点是依赖网络与系统编排；扩展样本中 6/25 家，占 24.0%。 | 科大讯飞 / 灵境智源 | 讯飞机器人大脑采用 MScape N1100/MS-N1100 端侧具身智脑设备，具体大脑模型未公开。 | N1100 以 Xenomai 硬实时和 EtherCAT 接口支撑控制，但未公开独立小脑模型。 | N1100 官方披露 1000 TOPS；用户会场口径为可按网络切换云+端或纯端侧，核心为 Jetson T5000、可选 T4000，其中 T5000 官方规格为 2070 FP4 TFLOPS、128GB、40-130W。 |  |
|  | 中国兵器 / 杭州智元研究院 | 具身大脑通用套件基于多模态大模型构建“感知-记忆-规划-执行-反思”自主决策闭环，模型参数未公开。 |  | 公开稿披露具身大脑通用套件和机动抗毁边缘云；用户会场观察为端侧大脑设备无网可用，但智能弱于云侧大脑。 |  |
|  | 优必选 UBTECH | Thinker 是最大 100B 的具身基座模型，Thinker-VLA 负责动作执行，Thinker-WM 是世界模型。 |  | 优必选将高实时能力放在端侧，云端承担慢推理、多机调度和模型管理；VLA 端侧显存需求从 64GB 优化至 32GB。 | VLA 推理效率提升 176%。 |
|  | 智元机器人 AGIBOT | GO-1/GO-2 是具身基座模型，GE-2 是世界模型，Genie Evolver 1.0 是闭环真机强化学习系统，参数未公开。 | Action Expert 负责精细动作执行，参数未公开。 | A3 Ultra 公开披露 700 TOPS 端侧具身处理器。 | 二手口径称 GO-1/GO-2 可在边缘端 10Hz-20Hz 推理，官方 Hz 未公开。 |
|  | 北京人形 / 天工 | Pelican-Unify 是统一表征具身世界模型，集成 VLM、action model 和 WFM；PelicanVLM 是 72B 视觉语言大模型，XR-1 是跨本体 VLA。 | 北京人形披露全身控制自主导航系统，但未公开参数。 | Pelican-Unify 已在天轶真机部署跑通；天工 Omni 采用端侧原生智能架构并支持大模型边缘部署。 |  |
| 云侧/平台/未明：公开资料未说明生产部署方式，或大脑更像平台/研究模型，优点是模型尺度和训练迭代空间更大，缺点是离线能力与实时性不确定；扩展样本中 11/25 家，占 44.0%。 | 银河通用 Galbot | LDA-1B 是 1B 隐式世界-动作基础模型，AstraBrain WAM 是世界动作模型，GraspVLA/GroceryVLA/TrackVLA 是技能 VLA。 | AstraBrain-WBC 0.5 是 8040 万参数全身实时运控小脑。 | AstraBrain-WBC 0.5 公开测试使用单张 RTX 4090，并支持 Jetson Orin onboard deployment；LDA-1B 生产部署算力未公开。 | AstraBrain-WBC 0.5 在 RTX 4090 上推理低于 1.5ms，可满足 50Hz 闭环。 |
|  | 智平方 AI2 Robotics | AlphaBrain 是具身大模型，GOVLA/FiS-VLA 是采用异构输入和异步频率的双系统 VLA，参数未公开。 |  |  |  |
|  | 自变量 X Square | WALL-B 是世界统一模型，Wall-OSS-0.5 是端到端预训练模型，参数未公开。 |  | 模型已上真机，端云分工未公开。 |  |
|  | 魔法原子 MagicLab | Magic-VLA K02 是通用具身大模型，采用高层任务理解与低层动作执行双系统；Magic-Mix 是世界模型，含 WAM 和 Creator。 | MagicBot Gen1 官方披露 motion control network、6D visual servoing 和 full-body imitation learning，但未公开具名小脑模型参数。 | MagicBot Gen1 官方规格为 8-core CPU + 100 TOPS AI Processor；Magic-VLA K02/Magic-Mix 生产部署算力未公开。 |  |
|  | 京东 JoyAI | JoyAI-RA 0.1 是 VLA 机器人自治基础模型，JoyAI-RA 0.5 是 VLWA 框架，包含 VLM、latent-action-conditioned world model 和 flow-matching action expert；JoyAI 矩阵含 7 个基础模型。 |  | 京东云/JoyInside 提供机器人生态与云侧能力，但未公开机器人本体端侧算力。 |  |

### 8.2 算力单元参考

| 算力单元/平台 | 明确数据 | 对应部署方式 | PPT 用法 |
|---|---|---|---|
| NVIDIA Jetson T5000 / Jetson AGX Thor | 2070 FP4 TFLOPS（稀疏）；128GB LPDDR5X；273GB/s；40-130W。 | 纯端侧/端云协同的大脑推理、VLA、VLM、世界模型。 | 说明端侧机器人“大脑”已进入 2 PFLOPS FP4、128GB 内存档位。 |
| NVIDIA Jetson T4000 | 1200 FP4 TFLOPS（稀疏）；64GB LPDDR5X；273GB/s；40-70W。 | 中高阶本体端侧推理。 | 可解释 MS-N1100/N1100 等设备的可选配置和功耗档。 |
| 灵境智源 MScape N1100 | 1000 TOPS；14 路传感器融合；4 路 EtherCAT；万兆网口；Xenomai 硬实时。 | 纯端侧具身智脑设备。 | 可作为“模型未披露，但端侧智脑硬件量化明确”的样本。 |
| 地瓜机器人 RDK S600 | 560 TOPS INT8；32/64GB LPDDR5；204.8GB/s；适配 3B 具身模型。 | 国产端侧大脑/边缘算力平台。 | 说明 3B 级模型已经可在国产端侧平台完成真机适配。 |
| 智元 A3 Ultra / 星海图 Nexo | A3 Ultra 公开口径 700 TOPS；Nexo 700 TOPS 为会场口径，待公开链接。 | 高阶人形本体端侧算力。 | 说明 700 TOPS 已成为 2026 高阶人形端侧算力锚点。 |

### 8.3 PPT 结论

1. 扩展样本显示，明确走纯端侧/本体端侧的为 8/25 家、占 32.0%；端云协同或可切换为 6/25 家、占 24.0%；仍未公开生产部署方式的为 11/25 家、占 44.0%。这说明“端侧化”已成为发布重点，但多数机器人厂商还没有把大脑模型的真实部署边界讲清。
2. NVIDIA 等算力平台正在把端侧大脑从 Orin 级推向 Thor 级：Jetson T4000 为 1200 FP4 TFLOPS，Jetson T5000/AGX Thor 为 2070 FP4 TFLOPS、128GB、40-130W；整机和边缘设备侧已有云深处/Aura 70 TOPS、星尘 T1/RDK S100 80 TOPS、宇树 G1-D Orin NX 100 TOPS、魔法原子 MagicBot Gen1 100 TOPS、地瓜 S600 560 TOPS、智元 A3 Ultra 700 TOPS、星海图 Nexo 700 TOPS 会场口径、星尘/TurboX IRB10 700 TOPS、灵境 N1100 1000 TOPS，端侧 VLA/VLM/世界模型的硬件门槛正在形成。
3. 端侧模型能力已经具备基础操作和低时延推理能力：MiniCPM-Robot 1.5B/0.9B 可在 H20 从 10Hz 提升到 37Hz，蚂蚁灵波 6B VLA 在 RTX 4090D 约 130ms，星海图 GigaWorld-Policy-0.5 在 RTX 4090 为 110ms、C++ 部署为 85ms，星尘 DuoCore-FS 支持 3B VLM 并达到 30Hz 全身 action-chunk generation，宇树 G1-D 系统遥操作延迟低于 100ms 且采样率 60Hz。端云协同仍适合 72B/100B 级大脑，端侧负责安全、离线和快反应，云侧负责更大参数、慢推理和多机调度。

### 8.4 官方来源补充清单

| 厂商 | WRC 官方展位/展品信息 | 公司/项目官方硬数据 | PPT 可引用点 |
|---|---|---|---|
| 蚂蚁灵波 Robbyant | 展位 C111；WRC 官方写明“基于蚂蚁灵波跨本体具身大模型”的物流、药房、工业三套展示方案。 | LingBot-VLA 2.0：6B、约 6 万小时预训练数据、20 种机器人构型、55 维动作空间、RTX 4090D 上约 130ms；LingBot-VA 约 5B DiT、18-24GB VRAM。 | “一脑多机”最适合做 PPT 例子：同一大脑驱动不同本体和不同场景。 |
| 银河通用 Galbot | 展位 C104；WRC 官方披露 AstraBrain WAM/WBC、GraspVLA/GroceryVLA/TrackVLA。 | LDA-1B：1B；AstraBrain-WBC 0.5：8040 万参数、RTX 4090 <1.5ms、50Hz 闭环、Jetson Orin onboard 部署。 | “大小脑硬指标最完整”：WAM/VLA + WBC 小脑 + 频率/算力。 |
| 优必选 UBTECH | 展位 C103；Walker S2 支持 3 分钟热插拔换电、7×24 小时运行；Walker C1 搭载大模型驱动多模态内核。 | Thinker 最大 100B；VLA 端侧显存从 64GB 优化到 32GB；推理效率提升 176%。 | “端侧工程化”：大模型能力压到机器人本体。 |
| 星海图 Galaxea | 展位 C210；Kengo 以高性能运动小脑与具身大脑为核心，R1 Lite 原生支持端侧模型部署。 | Fast-WAM 公开报道单步约 190ms；G0.5 复现计划开放权重/接口/评测/微调工具；Nexo 会场口径约 700 TOPS。 | “端侧算力+世界模型延迟”：Nexo 700TOPS、Fast-WAM 190ms。 |
| 自变量 X Square | 展位 C107；量子 1 Pro/量子 2 号搭载端到端具身智能基座大模型，量子 2 号最多 62 自由度。 | Wall-OSS-0.5 上真机测试 17 个任务；WALL-B 世界统一模型；参数/算力未公开。 | “统一模型路线”：视觉、语言、动作从分段走向统一。 |
| 宇树 Unitree | 展位 C312；G1、B2、Go2、H2，WRC 官方披露 G1 23-43 关节、H2 超高算力平台。 | UnifoLM-WMA-0 为开源 World-Model-Action 架构；公开报道称 UnifoLM-X1-0 工业级具身大模型已在自有工厂试点部署；G1-D 官方写 Orin NX 16GB 100 TOPS 高算力模组、8 核高性能 CPU、遥操作延迟低于 100ms、采样率 60Hz。 | “本体端侧平台+WMA/VLA 路线”：可补入一页 PPT 主表，但自研大脑参数量未公开。 |
| 傅利叶 Fourier | 展位 C229；GR-3 为 Care-bot，柔肤软包覆与全感交互系统。 | WRC 复盘报道称其框架连接 LLM、具身模型、导航规划和低级运控；参数/算力未披露。 | “通用控制栈”：把模型和本体适配层平台化。 |
| 云深处 Deep Robotics | WRC/WAIC 公开材料显示绝影 X30 进入黑芝麻智能 SesameX 机器人生态展示。 | 未检索到云深处具名大脑模型；黑芝麻 Aura 平台为 70 TOPS，公开材料称 Aura 正在云深处等伙伴产品中进入商用阶段；云深处 X30 官方页未披露芯片型号。 | “端侧算力有生态口径、模型未公开”：适合放入 PPT 但需标注来源边界。 |
| 魔法原子 MagicLab | 展位 C323；WRC 官方披露“通用大脑+模块化全栈产品”架构和原子万象大模型。 | Magic-VLA K02 为通用具身大模型，采用高层任务理解与低层动作执行双系统；Magic-Mix 世界模型包含 Magic-Mix WAM 和 Magic-Mix Creator；MagicBot Gen1 官方规格为 8-core CPU + 100 TOPS AI Processor。 | “模型名+端侧算力有公开信息，但模型参数未披露”：可补入 PPT 平台/未明类别。 |
| 北京人形 / 天工 | WRC 发布 Pelican-Unify 与天工 Omni；官方称 Pelican-Unify 集成 VLM、action model、WFM，并在天轶真机部署跑通。 | PelicanVLM 72B；Pelican-Unify 百万小时数据池；天工 Omni 端侧原生智能架构，支持大模型边缘部署。 | “统一表征世界模型 + 端侧原生本体”代表国家队路线。 |
| 中国兵器 / 杭州智元研究院 | 中国兵器牵头央企展区；公开稿披露“具身大脑通用套件”和“机动抗毁边缘云”。 | 具身大脑通用套件基于多模态大模型构建“感知-记忆-规划-执行-反思”闭环；模型参数未公开。 | 可作为弱网/无网端侧大脑设备样本，用户会场观察为“无网可用，智能弱于云侧”。 |
| 科大讯飞 / 灵境智源 | 灵境智源与科大讯飞 2025 年达成具身智能战略合作；灵境 N1100 为第三代人形机器人大脑。 | N1100 官方披露 1000 TOPS、14 路传感器融合、4 路 EtherCAT、Xenomai；用户会场观察为讯飞机器人大脑 MS-N1100 可按网络切换云+端/纯端侧，核心 Jetson T5000/可选 T4000。 | 端侧智脑硬件数据明确，但具体大脑模型名称未披露。 |
| 机器科学 RoboScience | WRC 展示 REX G1 和 Visics 通用具身大模型；公开报道称展台通过 API 调用部署于腾讯云的具身世界模型与通用操作模型。 | Visics 采用 VLOA 架构，内部由具身世界模型和通用操作模型双引擎驱动；另有报道称 REX G1 端侧搭载 2070 TFLOPS 算力。 | 可作为“端侧算力 + 云侧世界模型/操作模型”的端云协同样本。 |
| 京东 JoyAI | WRC 官方材料披露京东“机器人+AI 生态”，JoyInside 构建 AI Home 生态；京东近期发布 JoyAI 模型矩阵。 | JoyAI-RA 0.1 为 VLA 机器人自治基础模型；JoyAI-RA 0.5 为 VLWA 框架，包含 VLM、latent-action-conditioned world model 与 flow-matching action expert；公开材料未披露参数量和机器人本体算力。 | “模型框架明确、算力未明”：适合放入平台/未明类别。 |
| 星尘智能 Astribot | WRC 官方展位页披露星尘智能自研 Lumo 基座模型和 DuoCore 商业化部署框架，采用快慢双系统平衡复杂任务理解和实时动作控制。 | DuoCore-FS 为异步 Fast-Slow VLA，支持 3B 参数 VLM，并达到 30Hz 全身 action-chunk generation；TurboX IRB10 基于 Qualcomm Dragonwing IQ10，最高 700 TOPS，已在星尘产品上首个产品级落地；星尘 T1 已适配 RDK S100，S100 BPU 为 80 TOPS。 | “模型参数+频率+算力都有量化”：适合补入 PPT 纯端侧/本体端侧代表。 |

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
| 魔法原子 MagicLab | WAIC 发布 MagicBot X1/D1/T1 和 Magic-VLA K02，现场展示叠盒封胶、柔性衣物整理、行李箱收纳。 | Magic-VLA K02；Magic-Mix 世界模型包含 Magic-Mix WAM 和 Magic-Mix Creator。 | 参数未披露。 | MagicBot Gen1 官方规格为 8-core CPU + 100 TOPS AI Processor；Magic-VLA K02/Magic-Mix 的生产部署算力未公开。 | 可补入一页 PPT，但应归入“云侧/平台/未明”：有模型名和本体算力，缺模型参数和真实部署方式。 | https://autonews.gasgoo.com/articles/news/waic-2026-magiclab-launches-three-new-robot-products-embodied-intelligence-accelerates-entry-into-factories-and-public-scenarios-2079099620004175873；https://www.magiclab.top/en/human；https://www.gasgoo.com/robot/news/70456157-%E9%AD%94%E6%B3%95%E5%8E%9F%E5%AD%90%E5%8F%91%E5%B8%83%E8%87%AA%E7%A0%94%E4%B8%96%E7%95%8C%E6%A8%A1%E5%9E%8Bmagic-mix/ |
| 普渡机器人 Pudu | WAIC 首次线下展示 PUDU D7，并阐释“一脑多形”Physical Agent 全栈技术架构。 | PuduFM 1.0 具身智能基座模型；Pudu Agent OS。 | 模型参数未披露；累计出货超 13 万台、覆盖 85 个国家、5 万余家客户，每年超 5000 万小时实景运行数据回流。 | D7 最高负载 14kg、作业高度 2m、自主换电；公开 WAIC 材料未披露模型端侧算力。 | 可补“商用服务机器人数据飞轮”，但缺参数/算力，PPT 主表优先级低于蚂蚁/银河/智元/地瓜/MiniCPM。 | https://finance.sina.com.cn/roll/2026-07-17/doc-iniicqkw1429495.shtml；https://www.prnewswire.com/news-releases/pudu-embodied-unveils-the-next-generation-pudu-d7-opening-a-new-chapter-for-industrial-semi-humanoid-robotics-302786976.html |
| 千觉机器人 X-Touch | WAIC 展示 VTLA 触觉具身模型、XTac UMI G1 视触觉数据采集夹爪、触觉传感器矩阵。 | VTLA（视觉-触觉-语言-动作）模型；X-TouchMind V1；TacVerse 数据集。 | 参数未披露。 | 触觉传感器毫秒级响应；XTac UMI G1 同步采集视觉、触觉、位姿轨迹、夹爪状态等五维数据。 | 可补 System 0/1 的触觉闭环方向，但因参数算力不足，仅作趋势项。 | https://www3.xinhuanet.com/tech/20260720/6cbb5f1efd99431ab721ffce590e72f1/c.html；https://www.stcn.com/article/detail/4027332.html |

### 9.2 对一页 PPT 的更新建议

1. 如果只留 5 个“硬数据玩家/项目”：银河通用、蚂蚁灵波、智元、地瓜机器人/它石智航、MiniCPM-Robot。
2. 智元应从“参数算力未披露”调整为“模型参数未披露，但 A3 Ultra 端侧算力 700TOPS 已有 WAIC 媒体披露”。
3. 地瓜 S600 不是机器人整机厂，但它给了最清晰的国产端侧算力样本：560TOPS、32/64GB、高带宽、3B 具身模型 5 天真机部署。
4. MiniCPM-Robot 不是整机厂，但它是 WAIC 前后最适合说明“小参数、端侧、低延迟”的开源具身模型样本：1.5B/0.9B、H20 37Hz、Go2 本地 5+FPS。
5. 星海图 Nexo 700TOPS 仍应标注为“会场口径待补公开链接”；可用 G0.5/Fast-WAM/GigaWorld 的公开论文数据补足模型侧证据。

## 10. 扩展部署方式统计样本名单

统计口径：覆盖本报告在 WRC 2026、WAIC 2026 及厂商近期公开材料中纳入的机器人整机厂、具身大脑供应商和机器人模型项目；不把 NVIDIA、黑芝麻智能、摩尔线程、TurboX/创通联达这类纯算力或模组平台供应商计入占比，但在 PPT 和报告中作为算力演进参照。若厂商公开资料未说明生产部署方式，则归入“云侧/平台/未明”。

| 部署方式 | 数量 | 占比 | 参与统计的厂商/项目 |
|---|---:|---:|---|
| 纯端侧/本体端侧 | 8 | 32.0% | 蚂蚁灵波 Robbyant；MiniCPM-Robot / OpenBMB；地瓜机器人 / 它石智航；星海图 Galaxea；蔚蓝智能 BabyAlpha；宇树 Unitree；云深处 Deep Robotics；星尘智能 Astribot |
| 端云协同/可切换 | 6 | 24.0% | 优必选 UBTECH；智元机器人 AGIBOT；北京人形 / 天工；科大讯飞 / 灵境智源；中国兵器 / 杭州智元研究院；机器科学 RoboScience |
| 云侧/平台/未明 | 11 | 44.0% | 银河通用 Galbot；智平方 AI2 Robotics；自变量 X Square；影身智能 Shadow AI；章鱼动力 Octopus；傅利叶 Fourier Intelligence；逐际动力 LimX Dynamics；魔法原子 MagicLab；普渡机器人 Pudu；千觉机器人 X-Touch；京东 JoyAI |
