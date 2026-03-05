# DAD_reproduce
测试阶段
问题1 加载图片的键没有C，但用于测试的是C--用A，B替代在S/RDehazeNet的set_input

问题2 visdom连接失败--在另一个终端连接visdom服务器

训练阶段
一、训练CycleGAN
1.
  File "/home/wordma/桌面/实验复现/DA_dahazing/data/aligned_dataset.py", line 50, in __getitem__
    E_path = self.E_paths[C_ind]
IndexError: list index out of range
bug:(self.E_paths)是一个空列表
错误：unlabeled_depth文件夹为空

2.visdom连接问题
ConnectionRefusedError: [Errno 111] Connection refused

During handling of the above exception, another exception occurred:

改进思考：
1.怎么可视化训练过程，训练过程loss的变化或者有哪些指标是有观测价值的？
visdom
<img width="933" height="424" alt="image" src="https://github.com/user-attachments/assets/c8de6eb5-254b-438b-8964-4b15db1c972c" />

2.通过观测过程有哪些启发可以改进？如何改进？
1）学习率是不变的，可不可以改为退火余弦？loss会不会降低
2）
3.如何评估训练的结果好坏？
1）PSNR,SSIM
2）图像本身
4.消融实验
1）如果没有深度图，损失以及可视化效果如何？
