# DAFNet
数据结构
代码适配如下结构：

ExImage/
  BigGAN/
    train/
    test/
  CycleGAN/
    train/
    test/
  DALLE/
    train/
    test/
  Flux/
    train/
    test/
  Glide/
    train/
    test/
  LatentDM/
    train/
    test/
  Midjourney/
    train/
    test/
  SD14/
    train/
    test/
  SD15/
    train/
    test/
实验协议
每个 split 选择 5 类作为 known：

训练：只使用 known 类的 train
测试：使用 9 类的 test
known 类 test 用于闭集源模型归因
unknown 类 test 用于未知生成器拒识
这对应：

Train: 5 known generators / train
Test : 5 known generators / test + 4 unknown generators / test
修改路径
编辑：

configs/config.yaml
把：

data_root: "E:/genimage/ExImage"
改成你的真实路径。

单个 split 训练
python train.py --split split1
单个 split 多退化评估
python eval_degradation.py --split split1
跑所有 splits
python run_all_splits.py
只评估所有 splits
python run_all_splits.py --eval-only
汇总 Mean ± Std
python tools/summarize_results.py --results results
自动生成 splits
9 类每次选 5 类作为 known，理论共有 C(9,5)=126 个 splits。生成 5 个随机 splits：

python tools/generate_splits.py \
  --classes BigGAN CycleGAN DALLE Flux Glide LatentDM Midjourney SD14 SD15 \
  --known-size 5 \
  --num-splits 5 \
  --output configs/splits.yaml
生成全部 126 个 splits：

python tools/generate_splits.py \
  --classes BigGAN CycleGAN DALLE Flux Glide LatentDM Midjourney SD14 SD15 \
  --known-size 5 \
  --all \
  --output configs/splits.yaml
方法说明
训练时，对同一张图像生成多个退化版本：

clean / jpeg / resize / blur
它们作为 contrastive positive；不同原图作为 negative。分类损失用于 known 类源模型归因，对比损失用于学习退化不变特征。

支持的评估退化：

clean / jpeg / resize / blur / noise / webp / mixed
