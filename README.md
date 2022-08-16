# Amazon_Vine_Analysis

## Project Overview
The purpose of this project is to analyze customer review dataset from Amazon. Using [PySpark](https://spark.apache.org/docs/latest/api/python/), the ETL process is performed to extract the dataset, transform the data, and connect to an [AWS RDS](https://aws.amazon.com/rds/) instance. The transformed data is then loaded into pgAdmin. Finally, Pandas is used to determine if there is any bias towards favorable reviews from "[Amazon Vine](https://www.amazon.com/gp/vine/help)" members in the dataset.

## Data Source
The dataset is extracted from [Amazon's US Reviews Dataset](https://s3.amazonaws.com/amazon-reviews-pds/tsv/index.txt). From this list, the Video Game dataset is chosen for analysis. [Click here to download the full Video Game dataset](https://s3.amazonaws.com/amazon-reviews-pds/tsv/amazon_reviews_us_Video_Games_v1_00.tsv).

## What is Amazon Vine?
Amazon Vine is a program launched by Amazon.com that allows manufacturers and publishers to receive reviews for their products from a filtered group of Amazon customers, called "Vine Voices." These Vine Voices are chosen based on several criteria, including their total number of reviews and helpfulness of reviews. **In exchange for free products, these Vine Voices are required to publish a review.** [Amazon's Vine help guide](https://www.amazon.com/gp/vine/help) states that "Voices are not paid" and that Amazon welcomes an "honest opinion about the product."

## Extract, Transform, Load (ETL)  
The video game dataset is extracted into the following DataFrames: customers_df, products_df, review_id_df, and vine_df. After connecting to the AWS RDS instance, each of these DataFrames are written to the existing tables in pgAdmin. The password and url used to configure the settings for the RDS have been hidden for security, you will need to apply your own information in this section.

![postgres_table](https://github.com/Mishkanian/Amazon_Vine_Analysis/blob/main/README_images/postgres_tables.png)

For the purpose of this project, only the vine_table is necessary, which is exported from pgAdmin as vine.csv ([Download the vine.zip file here](https://github.com/Mishkanian/Amazon_Vine_Analysis/blob/main/vine.csv.zip)).

### Determining Review Bias

To determine if there is any review bias, Pandas is used to filter and create new DataFrames. This potion of the analysis is found in [Vine_Review_Analysis.ipynb](https://github.com/Mishkanian/Amazon_Vine_Analysis/blob/main/Vine_Review_Analysis.ipynb).

The vine.csv file is read in as DataFrame:

![vine_df](https://github.com/Mishkanian/Amazon_Vine_Analysis/blob/main/README_images/vine_df.png)

In the [first filter](https://github.com/Mishkanian/Amazon_Vine_Analysis/blob/main/README_images/high_votes_filter1.png), vine_df is filtered to only show rows where the number of total votes is greater than or equal to 20. Doing this will help pick reviews that more likely to be helpful and to avoid having division by zero errors. This filter is saved as a new DataFrame.
![first_filter](https://github.com/Mishkanian/Amazon_Vine_Analysis/blob/main/README_images/high_votes_filter1.png)

A [second filter](https://github.com/Mishkanian/Amazon_Vine_Analysis/blob/main/README_images/helpful_votes_filter2.png) (Filter #2) is then used on previous filter (Filter #1) to create a new DataFrame that retrieves all rows where the number of helpful votes divided by the total votes is greater than or equal to 50%.

![second filter](https://github.com/Mishkanian/Amazon_Vine_Analysis/blob/main/README_images/helpful_votes_filter2.png)

Finally, two more DataFrames are created to separate Filter #2 between reviews written as [part of the Vine program (paid)](https://github.com/Mishkanian/Amazon_Vine_Analysis/blob/main/README_images/yes_vine_df.png) and reviews [not part of the Vine program (unapid)](https://github.com/Mishkanian/Amazon_Vine_Analysis/blob/main/README_images/no_vine_df.png). After creating these final DataFrames, the following metrics are determined:
- The total number of reviews.
- The number of 5-star reviews.
- The percentage of 5-star reviews (Paid and Unpaid).

## Results
For the Video Game dataset:
- There are only 94 Vine reviews.
  - 48 of Vine reviews gave 5-stars.
  - Approximately **51.06% of Vine reviews were 5-stars.**
- There are 40,471 non-Vine reviews.
  - 15,663 non-Vine reviews gave 5-stars.
  - Approximately **38.70% of non-Vine reviews were 5-stars.**

## Summary
Based on this analysis, **there appears to be a positivity bias among Video Game reviews in the Vine program**. While only 38.70% of regular reviews gave 5-stars, 51.06% of Vine reviews gave 5-stars. 

However, it should be noted that the data present in this dataset is not reflective of a single product. This dataset contains a multitude of different hardware, software and accessories for different video game consoles. As a result of this large variety of products, this analysis cannot be applied to individual products, but rather the dataset as a whole. Furthermore, out of the 40,565 data points analyzed, only 0.23% are Vine reviews. This small amount of reviews is not significant enough to sway the overall rating of products listed for sale on Amazon.

### Recommendations for Further Analysis
One additional analysis that could be performed on this dataset to further research the possibility of positivity bias is to compare the average Vine review ratings to the average customer rating. If it is found that Vine customers have a higher average star rating than non-Vine customers, this might be an indication of positivity bias. 
