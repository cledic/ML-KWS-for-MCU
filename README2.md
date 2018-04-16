# Steps to build again the training

## I follow this steps to build the training with the default labels.

```
python train.py --model_architecture dnn --model_size_info 144 144 144 --dct_coefficient_count 10 --window_size_ms 40 --window_stride_ms 40 --learning_rate 0.0005,0.0001,0.00002 --how_many_training_steps 10000,10000,10000 --summaries_dir work/DNN/DNN1/retrain_logs --train_dir work/DNN/DNN1/training 

python test.py --model_architecture dnn --model_size_info 144 144 144 --dct_coefficient_count 10 --window_size_ms 40 --window_stride_ms 40 --learning_rate 0.0005,0.0001,0.00002 --how_many_training_steps 10000,10000,10000 --summaries_dir work/DNN/DNN1/retrain_logs --train_dir work/DNN/DNN1/training --checkpoint /home/embedded/ML-KWS-for-MCU/work/DNN/DNN1/training/best/dnn_8448.ckpt-29600

python freeze.py --model_architecture dnn --model_size_info 144 144 144 --dct_coefficient_count 10 --window_size_ms 40 --window_stride_ms 40 --learning_rate 0.0005,0.0001,0.00002 --how_many_training_steps 10000,10000,10000 --summaries_dir work/DNN/DNN1/retrain_logs --train_dir work/DNN/DNN1/training --checkpoint /home/embedded/ML-KWS-for-MCU/work/DNN/DNN1/training/best/dnn_8448.ckpt-29600 --output_file dnn.pb

# https://github.com/ARM-software/ML-KWS-for-MCU/blob/master/Deployment/Quant_guide.md
# https://github.com/ARM-software/ML-KWS-for-MCU/issues/19
#
python quant_test.py --model_architecture dnn --model_size_info 144 144 144 --dct_coefficient_count 10 --window_size_ms 40 --window_stride_ms 40 --checkpoint /home/embedded/ML-KWS-for-MCU/work/DNN/DNN1/training/best/dnn_8448.ckpt-29600 --act_max 32 0 0 0 0

#
# Copy the new weights.h file to the Deployment source folder:
cp weights.h /home/embedded/ML-KWS-for-MCU/Deployment/Source/DNN/dnn_weights.h

#
# Moify the dnn.cpp file as follow:
  const q7_t DNN::ip1_wt[IP1_WT_DIM]=fc1_W_0;
  const q7_t DNN::ip1_bias[IP1_OUT_DIM]=fc1_b_0;
  const q7_t DNN::ip2_wt[IP2_WT_DIM]=fc2_W_0;
  const q7_t DNN::ip2_bias[IP2_OUT_DIM]=fc2_b_0;
  const q7_t DNN::ip3_wt[IP3_WT_DIM]=fc3_W_0;
  const q7_t DNN::ip3_bias[IP3_OUT_DIM]=fc3_b_0;
  const q7_t DNN::ip4_wt[IP4_WT_DIM]=final_fc_0;
  const q7_t DNN::ip4_bias[OUT_DIM]=Variable_0;
#
# Thanks to Daisuke
# https://github.com/ARM-software/ML-KWS-for-MCU/issues/19
#

```
