```
def hough(G_hyst):
    '''Return Hough transform of G'''
    H, W = G_hyst.shape
    D = int(np.ceil(np.sqrt(H ** 2 + W ** 2)))

    d_theta = 1
    num_thetas = 180
    num_p = D  # (p represents rho)
    votes = np.zeros([num_p, num_thetas], dtype=np.int32)
    edge_table = np.where(G_hyst > 360)
    for y, x in zip(edge_table[0], edge_table[1]):
        for i in range(0, num_thetas, d_theta):
            theta = i * np.pi / num_thetas
            rho = int(x*np.cos(theta) + y*np.sin(theta))
            votes[rho, i] += 1

    rho_max, theta_max = votes.shape
    for rho in range(rho_max):
        for theta in range(theta_max):
            thetal = max(theta-1, 0)
            thetar = min(theta+2, theta_max)
            rhol = max(rho-1, 0)
            rhor = min(rho+2, rho_max)
            if np.max(votes[rhol:rhor, thetal:thetar]) == votes[rho, theta] and votes[rho, theta] != 0:
                pass
            else:
                votes[rho, theta] = 0

    result = G_hyst.copy()
    result = result[:, :, np.newaxis]
    result = np.tile(result, (1, 1, 3))
    index_x = np.argsort(votes.ravel())[::-1][:20]
    index_y = index_x.copy()
    thetas = index_x % 180
    rhos = index_y // 180
    for theta, rho in zip(thetas, rhos):
        i = theta / 180. * np.pi
        for x in range(W):
            if np.sin(i) != 0:
                y = rho/np.sin(i) - np.cos(i)/np.sin(i)*x
                y = int(y)
                if y < 0 or y > H-1:
                    continue
                result[y, x] = [255, 0, 255]
        for y in range(H):
            if np.cos(i) != 0:
                x = rho/np.cos(i) - np.sin(i)/np.cos(i)*y
                x = int(x)
                if x < 0 or x > W - 1:
                    continue
                result[y, x] = [255, 255, 0]

    return votes, result
```