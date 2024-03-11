```
import numpy as np  
  
X = np.array(  
            [[0.697,0.460],[0.774,0.376],[0.634,0.264],[0.608,0.318],[0.556,0.215],  
             [0.403,0.237],[0.481,0.149],[0.437,0.211],[0.666,0.091],[0.243,0.267],  
             [0.245,0.057],[0.343,0.099],[0.639,0.161],[0.657,0.198],[0.360,0.370],  
             [0.593,0.042],[0.719,0.103],[0.359,0.188],[0.339,0.241],[0.282,0.257],  
             [0.748,0.232],[0.714,0.346],[0.483,0.312],[0.478,0.437],[0.525,0.369],  
             [0.751,0.489],[0.532,0.472],[0.473,0.376],[0.725,0.445],[0.446,0.459]]  
            )  
# k = 3  
# u = np.array([[0.697,0.460],[0.774,0.376],[0.634,0.264]])  
k = 4  
u = np.array([[0.639,0.161],[0.657,0.198],[0.360,0.370],[0.483,0.312]])  
# k = 5  
# u = np.array([[0.751,0.489],[0.532,0.472],[0.473,0.376],[0.725,0.445],[0.446,0.459]])  
def k_means(X, k, u = None, iteration = 30):  
  
    # 从D中随机选择k个样本作为初始中心点  
    m, n = X.shape  
    if u.all() == None:  
        choose = np.random.randint(0, m, size = k)  
        u = X[choose]  
    u1 = np.copy(u)  
    step = 0  
  
    #repeat  
    while True:  
        step = step + 1  
        u2 = np.copy(u1)  
        dist = np.zeros((m, k))  
        for i in range(m):#计算样本与各初始中心的距离  
            for j in range(k):  
                dist[i, j] = np.sqrt( np.sum ( (X[i] - u1[j])**2 ) )  
        center = np.argmin(dist, axis = 1)#归类数组，argmin返回索引，找到每个点最近的中心，划入相应的簇  
        for j in range(k):  
            #计算新的均值  
            sum = np.array([0, 0])  
            count = 0.0  
            for i in range(m):#  
                if center[i] == j:  
                    sum = sum + X[i]  
                    count = count + 1  
            u1[j] = sum/count  
        if (u2 == u1).all() or step > iteration:#如果没变，或者迭代次数过高  
            break  
  
    fina = 0 #计算最小化平方误差  
    for j in range(k):  
        for i in range(m):  
            if center[i] == j:  
                fina = fina + ((X[i][0] - u1[j][0])**2 + (X[i][1] - u1[j][1])**2)  
  
    return u1, step, center, fina  
  
print(k_means(X, k, u, 30))
```

[改进参考](https://www.cnblogs.com/NoNameIsBeginning/p/13733072.html)