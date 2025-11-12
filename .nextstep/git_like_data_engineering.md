



using a git like approach,  the use of LLM in data engineering is easier (explore)

* https://github.com/datopian/metastore-lib (interesting for dataset publishing, the company have products just for that)
* Reference URL:
https://lakefs.io/blog/iceberg-catalog/

​

This project builds on ideas found in metastore-lib but is newer, actively developed, and focused on data lake metadata and dataset versioning. It fits well as a lightweight metastore alternative with branching features for Apache Iceberg proof-of-concept or development scenarios.

Summary:
For a simple or local-first metastore capable of versioning and branching metadata (and data) with Apache Iceberg integration, lakeFS is a strong, newer alternative to datopian/metastore-lib, designed specifically for data lakes and Iceberg catalogs.


# Git like for data engineering

There are several research papers, articles, and tools that follow a similar approach to Bauplan's paradigm of "Git for Data" and modular versioned pipelines with branching, Write-Audit-Publish patterns, and lakehouse versioning:

- "Git is for Data" paper by Y Low proposes the concept of XetHub, a system that retains the Git user experience and can scale for large datasets, implementing Git-like versioning semantics for data [https://www.cidrdb.org/cidr2023/papers/p43-low.pdf].[1]

- The blog "Git for Data - What, How and Why Now?" on lakeFS explains the benefits of Git-like operations for data engineering, such as branching, isolated development, commits, reverts, and collaborative version control of data [https://lakefs.io/blog/git-for-data/].[2]

- The "Write-Audit-Publish" data engineering pattern is described in detail in a lakeFS blog that discusses how this pattern ensures data quality and trust by writing data to staging, auditing it, then publishing it for consumption [https://lakefs.io/blog/data-engineering-patterns-write-audit-publish/].[3]

- Articles on Apache Iceberg branching and versioning show how lakehouse data management leverages Git-like branching to safely experiment, isolate jobs, backfill, and conduct safe development on data [https://www.starburst.io/blog/iceberg-branching-data-management/].[4]

- A blog by Dagster explains implementing the Write-Audit-Publish software design pattern in ETL pipelines for data reliability and quality [https://dagster.io/blog/python-write-audit-publish].[5]

- AWS blog shows how to build Write-Audit-Publish with Apache Iceberg branching and AWS Glue Data Quality for robust data ingestion and quality validation [https://aws.amazon.com/blogs/big-data/build-write-audit-publish-pattern-with-apache-iceberg-branching-and-aws-glue-data-quality/].[6]

- Bauplan itself published a blog on formal semantics of Git-for-Data branching, merging, and rollbacks highlighting how to wrap compute inside versioning and branching semantics to get consistency guarantees [https://www.bauplanlabs.com/post/git-for-data-formal-semantics-part-2-branching-merging-and-rollbacks].[7]

These references show Bauplan is closely aligned with emerging Git-for-Data research, lakehouse versioning, and Write-Audit-Publish design patterns in modern data engineering.

Urls for evidence:  
- https://www.cidrdb.org/cidr2023/papers/p43-low.pdf  
- https://lakefs.io/blog/git-for-data/  
- https://lakefs.io/blog/data-engineering-patterns-write-audit-publish/  
- https://www.starburst.io/blog/iceberg-branching-data-management/  
- https://dagster.io/blog/python-write-audit-publish  
- https://aws.amazon.com/blogs/big-data/build-write-audit-publish-pattern-with-apache-iceberg-branching-and-aws-glue-data-quality/  
- https://www.bauplanlabs.com/post/git-for-data-formal-semantics-part-2-branching-merging-and-rollbacks

[1](https://www.cidrdb.org/cidr2023/papers/p43-low.pdf)
[2](https://lakefs.io/blog/git-for-data/)
[3](https://lakefs.io/blog/data-engineering-patterns-write-audit-publish/)
[4](https://www.starburst.io/blog/iceberg-branching-data-management/)
[5](https://dagster.io/blog/python-write-audit-publish)
[6](https://aws.amazon.com/blogs/big-data/build-write-audit-publish-pattern-with-apache-iceberg-branching-and-aws-glue-data-quality/)
[7](https://www.bauplanlabs.com/post/git-for-data-formal-semantics-part-2-branching-merging-and-rollbacks)
[8](https://pmc.ncbi.nlm.nih.gov/articles/PMC3639880/)
[9](https://blog.okfn.org/2016/11/29/git-for-data-analysis-why-version-control-is-essential-collaboration-public-trust/)
[10](https://www.dasca.org/world-of-data-science/article/the-essentials-of-git-for-budding-data-scientists)
[11](https://docs.databricks.com/aws/en/getting-started/data-pipeline-get-started)
[12](https://www.reddit.com/r/github/comments/1ehu3tn/is_it_bad_or_frowned_upon_to_use_git_to_store_my/)
[13](https://www.linkedin.com/pulse/lakehouse-code-03-delta-live-tables-data-pipelines-olivier-soucy-fovte)
[14](https://www.reddit.com/r/dataengineering/comments/13vmdj6/what_is_writeauditpublish_and_why_is_it_useful/)
[15](https://www.dremio.com/blog/data-lakehouse-versioning-comparison-nessie-apache-iceberg-lakefs/)
[16](https://book.the-turing-way.org/reproducible-research/vcs/vcs-git-in-research/)
[17](https://learn.microsoft.com/en-us/fabric/data-engineering/lakehouse-git-deployment-pipelines)
[18](https://www.reddit.com/r/MicrosoftFabric/comments/1doy09r/git_versioning_strategies_and_deployment_pipelines/)
[19](https://www.tandfonline.com/doi/full/10.1080/10691898.2020.1848485)
[20](https://github.com/Mmodarre/Lakehouse_Plumber)
