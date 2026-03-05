# DAD_reproduce
测试阶段
问题1 加载图片的键没有C，但用于测试的是C--用A，B替代在S/RDehazeNet的set_input

问题2 visdom连接失败--在另一个终端连接visdom服务器

训练阶段
一、训练CycleGAN
  File "/home/wordma/桌面/实验复现/DA_dahazing/data/aligned_dataset.py", line 50, in __getitem__
    E_path = self.E_paths[C_ind]
IndexError: list index out of range
