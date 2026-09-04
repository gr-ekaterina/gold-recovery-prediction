# Data

The notebook can run without committed datasets. If the files are not found locally, it downloads them from the source URLs specified in the loading function.

To use local copies, place the following files in this directory:

```text
data/
├── gold_industry_train.csv
├── gold_industry_test.csv
└── gold_industry_full.csv
```

The datasets contain timestamped measurements from the flotation and purification process. Feature names follow this convention:

```text
[stage].[parameter_type].[parameter_name]
```

The `date` column identifies the measurement timestamp. The two prediction targets are:

- `rougher.output.recovery`;
- `final.output.recovery`.

The local CSV files are excluded by `.gitignore` and will not be committed accidentally.
