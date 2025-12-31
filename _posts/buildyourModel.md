lab-data-bucket-5c18cf80

sagemaker  jupeter studio



“算法（algorithm）”是训练模型的方式，
“模型（model）”是被训练出来的结果。


sagemaker.png


梯度向量

## 训练好放到s3

linear_regression = sage.estimator.Estimator(
    image,
    role,
    1,
    "ml.m4.xlarge",
    output_path=f"s3://lab-data-bucket-5c18cf80/output",
    sagemaker_session=sess,
)
linear_regression.fit(data_location)

## 部署
predictor = linear_regression.deploy(1, "ml.m5.xlarge", serializer=CSVSerializer())

## 推理

predictions = predictor.predict(x_data.values).decode("utf-8")

