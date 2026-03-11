# DAD_reproduce
测试阶段
问题1 加载图片的键没有C，但用于测试的是C--用A，B替代在S/RDehazeNet的set_input

问题2 visdom连接失败--在另一个终端连接visdom服务器
python -m visdom.server -port 8091/8095/8097
先激活conda
conda activate DAD
(DAD) wordma@wordma-B250M-D3H:~/桌面/实验复现/DA_dahazing$ python -m visdom.server -port 8094


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
2）损失可以改进
3.如何评估训练的结果好坏？
1）PSNR,SSIM
2）图像本身
3）增加评估指标：LPIPS、FID（生成效果）、'NIQE'、'BRISQUE'
4.消融实验
1）如果没有深度图，损失以及可视化效果如何？


其他思考：
问题：每次做测试的时候想从几千张图里找出作者文中出现的图测试，只能一个个找，能不能有现成的app,导入所有的图片然后输入文字查找？
还是自己训一个CLIP（或微调）？


/home/wordma/桌面/实验复现/DA_dahazing/checkpoints/run_cyclegan
/home/wordma/桌面/实验复现/DA_dahazing/datasets/dehazing
/home/wordma/下载/tropy-1.17.3-x64

二、训练Train Fr using the pretrained CycleGAN
python train.py  --dataroot ./datasets/dehazing --name run_fr_depth --lambda_Dehazing 10 --lambda_Dehazing_DC 1e-2 --lambda_Dehazing_TV 1e-2 --learn_residual --resize_or_crop crop --display_freq 100 --print_freq 100 --display_port 8090  --epoch_count 1 --niter 90 --niter_decay 0 --fineSize 256 --no_html --batchSize 2   --gpu_id 3 --update_ratio 1 --unlabel_decay 0.99 --save_epoch_freq 1 --model RDehazingnet --g_s2r_premodel ./checkpoints/run_cyclegan/netG_A.pth  
注意加载的模型是G不是D
完整epoch：
python train.py  --dataroot ./datasets/dehazing --name run_fr_depth --lambda_Dehazing 10 --lambda_Dehazing_DC 1e-2 --lambda_Dehazing_TV 1e-2 --learn_residual --resize_or_crop crop --display_freq 100 --print_freq 100 --display_port 8090  --epoch_count 1 --niter 90 --niter_decay 0 --fineSize 256 --no_html --batchSize 2   --gpu_id 0 --update_ratio 1 --unlabel_decay 0.99 --save_epoch_freq 1 --model RDehazingnet --g_s2r_premodel ./checkpoints/run_cyclegan/latest_netG_A.pth  

测试小epoch
python train.py  --dataroot ./datasets/dehazing --name run_fr_depth --lambda_Dehazing 10 --lambda_Dehazing_DC 1e-2 --lambda_Dehazing_TV 1e-2 --learn_residual --resize_or_crop crop --display_freq 100 --print_freq 100 --display_port 8090  --epoch_count 1 --niter 3 --niter_decay 0 --fineSize 256 --no_html --batchSize 2   --gpu_id 0 --update_ratio 1 --unlabel_decay 0.99 --save_epoch_freq 1 --model RDehazingnet --g_s2r_premodel ./checkpoints/run_cyclegan/latest_netG_A.pth  



三、Train Fs using the pretrained CycleGAN
python train.py  --dataroot ./datasets/dehazing --name run_fs_depth --lambda_Dehazing 10 --lambda_Dehazing_DC 1e-2 --lambda_Dehazing_TV 1e-2 --learn_residual --resize_or_crop crop --display_freq 100 --print_freq 100 --display_port 8094  --epoch_count 1 --niter 90 --niter_decay 0 --fineSize 256 --no_html --batchSize 2   --gpu_id 0 --update_ratio 1 --unlabel_decay 0.99 --save_epoch_freq 1 --model SDehazingnet --g_r2s_premodel ./checkpoints/run_cyclegan/netG_B.pth 




test datasets
SOTS :SYN
Hazerd : SYN + REAL？
https://labsites.rochester.edu/gsharma/research/computer-vision/hazerd/
https://docs.google.com/forms/d/e/1FAIpQLSdCz7X7wnyJo3A_CRBZ5Aqd_ZOAcNEPUesg88Lnqd2XSYpZNw/viewform
HazeRD
contains fifteen real outdoor scenes, for each of which five different weather conditions are simulated. 
As opposed to prior datasets that made use of synthetically generated images or indoor images with unrealistic parameters for haze simulation, 
our outdoor dataset allows for more realistic simulation of haze with parameters that are physically realistic and justified by scattering theory. 
All images are of high resolution, typically six to eight megapixels. 

<img width="1629" height="780" alt="image" src="https://github.com/user-attachments/assets/aa38ef77-af0e-46a0-a347-0055854992d9" />
<img width="1590" height="566" alt="image" src="https://github.com/user-attachments/assets/aa158ba8-b736-4dbc-b36a-6a29103de0c3" />
<img width="1784" height="680" alt="image" src="https://github.com/user-attachments/assets/cf7866b3-7812-454b-98dc-189aed01de46" />
 天空，边缘
<img width="1720" height="633" alt="image" src="https://github.com/user-attachments/assets/984e968c-0115-44f1-8f5d-899c9f693857" />
伪影
<img width="1620" height="782" alt="image" src="https://github.com/user-attachments/assets/6a52954f-f1b0-44e1-80b9-82151c061414" />
最后一次训练
<img width="1033" height="421" alt="image" src="https://github.com/user-attachments/assets/83c8b484-3ef8-4ca9-a5af-22a962c5a641" />
S_dehazing
<img width="1036" height="413" alt="image" src="https://github.com/user-attachments/assets/09b07124-fe31-4fb5-8bf6-db63d00cc086" />
D
<img width="1030" height="415" alt="image" src="https://github.com/user-attachments/assets/67d31322-fd5a-4f14-8154-c9f166879b72" />
TV DCP


四、Train DA_dehazing using the pretrained Fr, Fs and CycleGAN.
python train.py  --dataroot ./datasets/dehazing --name run_danet_depth --epoch_count 1 --niter 50 --lambda_S 1 --lambda_R 1 --lambda_identity 0.1 --lambda_Dehazing 10 --lambda_Dehazing_Con 0.1 --lambda_Dehazing_DC 1e-2 --lambda_Dehazing_TV 1e-3 --learn_residual --resize_or_crop crop --display_freq 100 --print_freq 100 --display_port 8094 --niter_decay 0 --fineSize 256 --no_html --batchSize 2   --gpu_id 0 --update_ratio 1 --unlabel_decay 0.99 --save_epoch_freq 1 --model danet --S_Dehazing_premodel ./checkpoints/run_fs_depth/netS_Dehazing.pth --R_Dehazing_premodel ./checkpoints/run_fr_depth/netR_Dehazing.pth --g_s2r_premodel ./checkpoints/run_cyclegan_depth/netG_A.pth --g_r2s_premodel ./checkpoints/run_cyclegan/netG_B.pth --d_r_premodel ./checkpoints/run_cyclegan/netD_A.pth --d_s_premodel ./checkpoints/run_cyclegan/netD_B.pth
<img width="1460" height="517" alt="image" src="https://github.com/user-attachments/assets/be71099b-9c57-438e-a1e3-edea1c9aea57" />
含文字

<img width="1409" height="574" alt="image" src="https://github.com/user-attachments/assets/5c84e8ec-40b5-4113-a78e-2bbdc115a1e8" />




后续各模块的PSNR、SSIM、无监督指标测量  +  消融
