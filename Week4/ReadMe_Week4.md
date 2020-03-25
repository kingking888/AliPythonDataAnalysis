### 任务与要求
    1 任务：分析这里的数据，针对这个数据集构建一个分析违约率的分类器
    2 具体要求：
        2.1 创建多种分类器，综合比较效果
        2.2 掌握 GridSearchCV ，优化算法模型的参数
        2.3 使用 Pipeline 管道机制进行流水线作业。因为在做分类之前，我们还需要一些准备过程，比如数据规范化，或者数据降维等。
    3 交付：代码文件放在github 给我一个链接
    4 任务简要分解：
        4.1 做分类的时候往往都是有步骤的，比如先对数据进行规范化处理，你也可以用 PCA 方法（一种常用的降维方法）对数据降维，最后使用分类器分类。
        4.2 Python 有一种 Pipeline 管道机制。管道机制就是让我们把每一步都按顺序列下来，从而创建 Pipeline 流水线作业。
            每一步都采用 (‘名称’, 步骤) 的方式来表示 
    5 特征值与目标值的说明：
            ID  用户ID
            LIMIT_BAL 花呗透支额度
            Sex  男1女2
            education 研究生1 本科2 高中3 其他4
            marriage 已婚1 单身2 其他3
            pay_n 内部划分的不同时期的客户还款情况 不用管时期怎么划分
            BILL_AMT 内部划分的不同时期的账单金额
            default.payment.next.month 违约1 守约 0
    6。 分类问题：分类是一种基于一个或多个自变量确定因变量所属类别的技术。
    
### STEP 1: 导入数据与划分数据集
    1。导入数据：使用 pandas.read_csv('https://.....', header='infer', index_col=None)
    2。随机抽样：DataFrame.sample(n=None, frac=None, replace=False, weights=None, random_state=None, axis=None)[source]
            n=3：提取3行数据列表
            frac=0.8： 抽取其中80%
            random_state=None：取得数据不重复
            random_state=1：可以取得重复数据
            axis：选择抽取数据的行还是列
                    axis=0: 在行中随机抽取n行
                    axis=1: 在列中随机抽取n列
            
    3。划分数据集：第一列 id 列给去掉，然后最后一列 default.payment.next.month 列 作为目标值，其他列作为特征值
            data=pd.read_excel("../data/yanben.xls");
            print(data.head())
            #iloc只能用数字索引，不能用索引名
            #print(data.iloc[:,0:4])
            ##loc只能通过index和columns来取，不能用数字
            #print(data.loc[0:1,["序号","颜色","形状","重量"]])
            #print(data['类别'])
            x_data=data.iloc[:,0:4];
            x_target=data['类别'];

### STEP 2 ：特征工程、数据预处理 - 数据规范化与数据降维
    1。数据规范化：
        1.1 归一化: sklearn.preprocessing.MinMaxScaler通过对原始数据进行变换把数据映射到(默认为[0,1])之间.
                什么时候进行归一化？归一化的作用？:当三个特征同等重要的时候，进行归一化, 归一化使得某一个特征对最终结果不会造成更大的影响。
                最大值与最小值非常容易受异常点影响，所以这种方法鲁棒性较差，只适合传统精确小数据场景。
        1.2 标准化: 通过对原始数据进行变换把数据变换到均值为0,方差为1范围内.
                在已有样本足够多的情况下比较稳定，适合现代嘈杂大数据场景。
    2。数据降维 - 主成分分析PCA：把高维数据转化为低维数据 -> 压缩数据，降低复杂度，损失少量信息
        2.1 PCA降维做的事情：找到一个合适的直线，通过一个矩阵运算得出主成分分析的结果
        2.2 API： sklearn.decomposition.PCA(n_components=None)
            2.2.1 n_components: 小数代表保留多少百分比的信息；整数代表减少到多少特征属性
            2.2.2 PCA.fit_transform(X): numpy array格式的数据 [n_samples, n_features]

### STEP 3 : 几大分类模型的算法原理及应用的简要说明
    1。KNN(K-nearest Neighbor, K近邻分类算法)：
        1.1 K-NN算法是一种最简单的分类算法，通过识别被分成若干类的数据点，以预测新样本点的分类。
            K-NN是一种非参数的算法，是“懒惰学习”的著名代表，它根据相似性（如，距离函数）对新数据进行分类。
    2。SVM(Support Vector Machine,支持向量机)：
        2.1 支持向量机既可用于回归也可用于分类。它基于定义决策边界的决策平面。决策平面（超平面）可将一组属于不同类的对象分离开。
        2.2 在支持向量的帮助下，SVM通过寻找超平面进行分类，并使两个类之间的边界距离最大化。   
    3。朴素贝叶斯算法：
        3.1 朴素贝叶斯分类器建立在贝叶斯定理的基础上，基于特征之间互相独立的假设
            （假定类中存在一个与任何其他特征无关的特征，则 P(A, B) = P(A) * P(B) ）。
            即使这些特征相互依赖，或者依赖于其他特征的存在，朴素贝叶斯算法都认为这些特征都是独立的。这样的假设过于理想，朴素贝叶斯因此而得名。  
    4。DT(Decision Tree, 决策树分类算法)：
        4.1 决策树以树状结构构建分类或回归模型。它通过将数据集不断拆分为更小的子集来使决策树不断生长。
            最终长成具有决策节点（包括根节点和内部节点）和叶节点的树。
            最初决策树算法它采用采用Iterative Dichotomiser 3（ID3）算法来确定分裂节点的顺序。
        4.2 信息熵和信息增益用于被用来构建决策树。
            4.2.1 信息熵是衡量元素无序状态程度的一个指标，即衡量信息的不纯度。直观上说地理解，信息熵表示一个事件的确定性程度。
                    信息熵度量样本的同一性，如果样本全部属于同一类，则信息熵为0；如果样本等分成不同的类别，则信息熵为1。    
            4.2.2 信息增益测量独立属性间信息熵的变化。它试图估计每个属性本身包含的信息，构造决策树就是要找到具有最高信息增益的属性(即纯度最高的分支)。
                    采用信息熵进行节点选择时，通过对该节点各个属性信息增益进行排序，选择具有最高信息增益的属性作为划分节点，过滤掉其他属性。
        4.3 决策树模型存在的一个问题是容易过拟合。因为在其决策树构建过程中试图通过生成长一棵完整的树来拟合训练集，因此却降低了测试集的准确性。
            通过剪枝技术可以减少小决策树的过拟合问题。
    5。Logistic Regression（逻辑斯谛回归算法）：
        4.1 逻辑斯谛回归类似于线性回归，用于预测二分类的输出。适用于因变量不是一个数值字的情况 (例如，一个“是/否”的响应)。
            它虽然被称为回归，但却是基于根据回归的分类，将因变量分为两类
        4.2 首先对变量之间的关系进行线性回归以构建模型（sigmoid函数 [0, 1]），分类的阈值假设为0.5, 
            P = 1 / (1 + e^(-y)),然后将Logistic函数应用于回归分析，得到两类的概率。
            该函数给出了事件发生和不发生概率的对数。最后，根据这两类中较高的概率对变量进行分类。
    
    ===============================================================================================
    分类的集成算法：集成算法是一个模型组。从技术上说，集成算法是单独训练几个有监督模型，并将训练好的模型以不同的方式进行融合，从而达到最终的得预测结果。
                    集成后的模型比其中任何一个单独的模型都有更高的预测能力。
    6。RF(Random Forrest, 随机森林分类算法：
        6.1 随机森林分类器是一种基于装袋（bagging）的集成算法，即自举助聚合法(bootstrap aggregation)。
            集成算法结合了多个相同或不同类型的算法来对对象进行分类（例如，SVM的集成，基于朴素贝叶斯的集成或基于决策树的集成）。
            解释：训练集 -> 多个子训练集 -> 得到多个基模型 -> 投票表决 -> 得出结果
            集成的基本思想是算法的组合提升了最终的结果。
        6.2 深度太大的决策树容易受过拟合的影响。但是随机森林通过在随机子集上构建决策树防止过拟合，
            主要原因是它会对所有树的结果进行投票的结果是所有树的分类结果的投票，从而消除了单棵树的偏差。
        6.3 随机森林在决策树生增长的同时为模型增加了额外的随机性。它在分割节点时，不是搜索全部样本最重要的特征，而是在随机特征子集中搜索最佳特征。
            这种方式使得决策树具有多样性，从而能够得到更好的模型。
    
    7。Gradient Boosting：
        7.1 梯度提升分类器是一种提升集成算法。提升(boosting)算法是为了减少偏差而对弱分类器的而进行的一种集成方法。
            与装袋（bagging）方法构建预测结果池不同，提升算法是一种分类器的串行方法，它把每个输出作为下一个分类器的输入。
            通常，在装袋算法中，每棵树在原始数据集的子集上并行训练，并用所有树预测结果的均值作为模型最终的预测结果；
            梯度提升模型，采用串行方式而非并行模式获得预测结果。每棵决策树预测前一棵决策树的误差，因而使误差获得提升。
        7.2 梯度提升树的工作流程:
                使用浅层决策树初始化预测结果。
                    计算残差值（实际预测值）。    
                    构建另一棵浅层决策树，将上一棵树的残差作为输入进行预测。               
                    用新预测值和学习率的乘积作为最新预测结果，更新原有预测结果。             
                重复步骤2-4，进行一定次数的迭代（迭代的次数即为构建的决策树的个数）。
    
### STEP 3.1 ：Pipeline和GridSearchCV结合使用 -> 优化算法模型的参数、操作流程自动化
    1。pipeline 简单使用代码示例：
            from sklearn.pipeline import Pipeline
            estimators = [
                Pipeline([('sc', StandardScaler()),('pca',PCA(n_components=2)), ('knn',KNeighborsClassifier(n_neighbors=5))]),
                Pipeline([('ss', StandardScaler()),('svc', SVC())]),
                Pipeline([('ss', StandardScaler()),('svc', SVC())])
            ]
    2。GridSearchCV 的应用示例：
        GridSearchCV是用交叉验证选出最优参数：
            其中第一个参数是pipeline，
            第二个参数param_grid是关于参数多个选择的字典。
            第三个参数cv是交叉验证的折数（例如5折交叉验证(5-fold cross validation)，
                将数据集分成5份，轮流将其中4份做训练1份做验证，5次的结果的均值作为对算法精度的估计）

        # 2.1 先设置参数字典： 字典中的key是属性的名称，value是可选的参数列表，需要注意下划线是两个
            parameters_knn = {'n_neighbors': [i for i in range(1, 11)],
                          'weights': ['uniform', 'distance'],
                          'leaf_size': [20, 30, 40, 50]
                         }
        # 2.2 获取模型并设置参数
            # GridSearchCV: 进行交叉验证，选择出最优的参数值出来
            # 第一个输入参数：进行参数选择的模型，
            # param_grid： 用于进行模型选择的参数字段，要求是字典类型；cv: 进行几折交叉验证
            estimator_knn = GridSearchCV(estimators[0], param_grid=parameters_knn, cv=5, n_jobs=1) #选择第一个模型, 并传入对应的参数
            # 模型训练-网格搜索
            estimator_knn.fit(x_train, y_train)
            
### STEP 4: 模型评估与模型测试：
    1。knn_y_predict = estimator_knn.predict(x_test)
           estimator_knn.best_score_
           estimator_knn.best_params_
    2。尝试传入真实的样本 丢给模型进行预测，并输出结果
    
### STEP 4.1: 分类器的性能评价原理
    1。混淆矩阵：混淆矩阵是一张表，这张表通过对比已知分类结果的测试数据的预测值和真实值表来描述衡量分类器的性能。
        在二分类的情况下，混淆矩阵是展示预测值和真实值四种不同结果组合的表。(TN) FP FN (TP)
            FN：被错误的分为 负类 的数据，（即 真实为 正，预测为 负）。
            TP：被正确的分为 正类 的数据，（即 真实为 正，预测也为 正）。 
        1.1 假正例&假负例：假正例和假负例用来衡量模型预测的分类效果。假正例是指模型错误地将负例预测为正例。假负例是指模型错误地将正例预测为负例。
                主对角线的值越大（主对角线为真正例和真负例），模型就越好；副对角线给出模型的最差预测结果。    
    2。准确率是模型预测正确的部分。Accuracy = (TP + TN) / (TP + TN + FP + FN)
        当数据集不平衡，也就是正样本和负样本的数量存在显著差异时，单独依靠准确率不能评价模型的性能。精度和召回率是衡量不平衡数据集的更好的指标。
        精度是指在所有预测为正例的分类中，预测正确的程度为正例的效果。精度越高越好。精度 = TP / (TP + FP)
        召回率是指在所有预测为正例（被正确预测为真的和没被正确预测但为真的）的分类样本中，召回率是指预测正确的程度。它，也被称为敏感度或真正率（TPR）。
            召回率 = TP / (TP + FN)
    3。F-1值：通常实用的做法是将精度和召回率合成一个指标F-1值更好用，特别是当你需要一种简单的方法来衡量两个分类器性能时。
        F-1值是精度和召回率的调和平均值。F1 = 2 * 精度 * 召回率 / （精度 + 召回率）
        普通的通常均值将所有的值平等对待，而调和平均值给予较低的值更高的权重，从而能够更多地惩罚极端值。
        所以，如果精度和召回率都很高，则分类器将得到很高的F-1值。
    4。接受者操作曲线（ROC）和曲线下的面积（AUC）
        ROC曲线是衡量分类器性能的一个很重要指标，它代表模型准确预测的程度。
        ROC曲线通过绘制真正率TP和假正率FP的关系来衡量分类器的敏感度。
        如果分类器性能优越，则真正率将增加，曲线下的面积会接近于1.如果分类器类似于随机猜测，真正率将随假正率线性增加。AUC值越大，模型效果越好。
    5。累积精度曲线 CAP代表一个模型沿y轴为真正率的累积百分比与沿x轴的该分类样本累积百分比。
        CAP不同于接受者操作曲线（ROC，绘制的是真正率与假正率的关系）。与ROC曲线相比，CAP曲线很少使用。
    
### 参考资源
    1。sklearn中pipeline的实现,及GridSearchCV寻找最优参数   https://blog.csdn.net/qq_34211618/article/details/103685975
    2。pandas系列 read_csv 与 to_csv 方法各参数详解    https://blog.csdn.net/u010801439/article/details/80033341
    3。基于SVM、Pipeline、GridSearchCV的鸢尾花分类 https://blog.csdn.net/xiaosa_kun/article/details/84868406
    4。数据样本，特征值，目标值，按比例划分    https://blog.csdn.net/u011066470/article/details/104447001
    5。pandas数据处理基础——筛选指定行或者指定列的数据   https://www.cnblogs.com/gangandimami/p/8983323.html
    6。sklearn中的Pipeline：    https://www.cnblogs.com/wuliytTaotao/p/9329695.html
    7。一文读懂机器学习分类算法（附图文详解）   https://blog.csdn.net/Datawhale/article/details/100788726
