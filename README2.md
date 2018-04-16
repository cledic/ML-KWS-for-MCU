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
```

## Copy the new weights.h file to the Deployment source folder:
```
cp weights.h /home/embedded/ML-KWS-for-MCU/Deployment/Source/DNN/dnn_weights.h
```

## Modify the dnn.cpp file as follow:
``` C
  const q7_t DNN::ip1_wt[IP1_WT_DIM]=fc1_W_0;
  const q7_t DNN::ip1_bias[IP1_OUT_DIM]=fc1_b_0;
  const q7_t DNN::ip2_wt[IP2_WT_DIM]=fc2_W_0;
  const q7_t DNN::ip2_bias[IP2_OUT_DIM]=fc2_b_0;
  const q7_t DNN::ip3_wt[IP3_WT_DIM]=fc3_W_0;
  const q7_t DNN::ip3_bias[IP3_OUT_DIM]=fc3_b_0;
  const q7_t DNN::ip4_wt[IP4_WT_DIM]=final_fc_0;
  const q7_t DNN::ip4_bias[OUT_DIM]=Variable_0;
```

## Thanks to Daisuke
## https://github.com/ARM-software/ML-KWS-for-MCU/issues/19


## To compile again the STM32F7 discovery firmware execute:
```
cd /home/embedded/ML-KWS-for-MCU/Deployment/kws_realtime_test
python -m mbed compile -m DISCO_F746NG -t GCC_ARM   --source . --source ../Source --source ../Examples/realtime_test   --source ../CMSIS_5/CMSIS/NN/Include --source ../CMSIS_5/CMSIS/NN/Source   --source ../CMSIS_5/CMSIS/DSP/Include --source ../CMSIS_5/CMSIS/DSP/Source   --source ../CMSIS_5/CMSIS/Core/Include   --profile ../release_O3.json   -j 8

```

# Steps to rebuild the training using different sounds
```
python train.py --model_architecture dnn --model_size_info 144 144 144 --dct_coefficient_count 10 --window_size_ms 40 --window_stride_ms 40 --learning_rate 0.0005,0.0001,0.00002 --how_many_training_steps 10000,10000,10000 --summaries_dir work2/DNN/DNN1/retrain_logs --train_dir work2/DNN/DNN1/training --wanted_words=car_horn,cat,clapping,clock_alarm,dog,door_wood_knock,glass_breaking,siren,train,stop


python test.py --model_architecture dnn --model_size_info 144 144 144 --dct_coefficient_count 10 --window_size_ms 40 --window_stride_ms 40 --learning_rate 0.0005,0.0001,0.00002 --how_many_training_steps 10000,10000,10000 --summaries_dir work2/DNN/DNN1/retrain_logs --train_dir work2/DNN/DNN1/training --wanted_words=car_horn,cat,clapping,clock_alarm,dog,door_wood_knock,glass_breaking,siren,train,stop --checkpoint /home/embedded/ML-KWS-for-MCU/work2/DNN/DNN1/training/best/dnn_9013.ckpt-16000

python freeze.py --model_architecture dnn --model_size_info 144 144 144 --dct_coefficient_count 10 --window_size_ms 40 --window_stride_ms 40 --learning_rate 0.0005,0.0001,0.00002 --how_many_training_steps 10000,10000,10000 --summaries_dir work2/DNN/DNN1/retrain_logs --train_dir work2/DNN/DNN1/training --wanted_words=car_horn,cat,clapping,clock_alarm,dog,door_wood_knock,glass_breaking,siren,train,stop --checkpoint /home/embedded/ML-KWS-for-MCU/work2/DNN/DNN1/training/best/dnn_9013.ckpt-16000 --output_file dnn.pb

# Così ha funzionato, ma devo capire cosa sono i parametri di act_max
# https://github.com/ARM-software/ML-KWS-for-MCU/blob/master/Deployment/Quant_guide.md
# https://github.com/ARM-software/ML-KWS-for-MCU/issues/19
#
python quant_test.py --model_architecture dnn --model_size_info 144 144 144 --dct_coefficient_count 10 --window_size_ms 40 --window_stride_ms 40 --checkpoint /home/embedded/ML-KWS-for-MCU/work2/DNN/DNN1/training/best/dnn_9013.ckpt-16000 --act_max 32 0 0 0 0

python label_wav.py --wav /tmp/speech_dataset/door_wood_knock/2-120218-B-30.wav --graph ./dnn.pb --labels ./work2//DNN/DNN1/training/dnn_labels.txt --how_many_labels 1


#
# Copiare il nuovo file nel seguente PATH e modificare il file dnn.cpp come riportato di seguito:
cp weights.h /home/embedded/ML-KWS-for-MCU/Deployment/Source/DNN/dnn_weights.h
#
# Modificare il file dnn.cpp come da indicazioni:
# const q7_t DNN::ip1_wt[IP1_WT_DIM]=fc1_W_0;
# const q7_t DNN::ip1_bias[IP1_OUT_DIM]=fc1_b_0;
# const q7_t DNN::ip2_wt[IP2_WT_DIM]=fc2_W_0;
# const q7_t DNN::ip2_bias[IP2_OUT_DIM]=fc2_b_0;
# const q7_t DNN::ip3_wt[IP3_WT_DIM]=fc3_W_0;
# const q7_t DNN::ip3_bias[IP3_OUT_DIM]=fc3_b_0;
# const q7_t DNN::ip4_wt[IP4_WT_DIM]=final_fc_0;
# const q7_t DNN::ip4_bias[OUT_DIM]=Variable_0;
#
# https://github.com/ARM-software/ML-KWS-for-MCU/issues/19
#

python label_wav.py --wav /tmp/speech_dataset/door_wood_knock/2-120218-B-30.wav --graph ./dnn.pb --labels ./work2//DNN/DNN1/training/dnn_labels.txt --how_many_labels 1

#
cd /home/embedded/ML-KWS-for-MCU/Deployment/kws_realtime_test
python -m mbed compile -m DISCO_F746NG -t GCC_ARM   --source . --source ../Source --source ../Examples/realtime_test   --source ../CMSIS_5/CMSIS/NN/Include --source ../CMSIS_5/CMSIS/NN/Source   --source ../CMSIS_5/CMSIS/DSP/Include --source ../CMSIS_5/CMSIS/DSP/Source   --source ../CMSIS_5/CMSIS/Core/Include   --profile ../release_O3.json   -j 8

```
