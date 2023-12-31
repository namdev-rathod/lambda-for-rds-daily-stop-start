# Lambda Code For Daily Scheduler for RDS Stop and Start
 - Create Lambda Function With Nodejs 18 version
 - Use AWS-SDK package as a layers features to support this lambda function code
 - Configure Lambda Environment Varialbes for DB_CLUSTER_IDENTIFIER = your rds identifier name

``` "use strict";
const AWS = require("aws-sdk");
exports.handler = async (event) => {
    const rds = new AWS.RDS();
    const dbClusterIdentifier = process.env.DB_CLUSTER_IDENTIFIER;
    // Determine if the Aurora cluster is currently stopped or running
    const clusterDetails = await rds.describeDBClusters({ DBClusterIdentifier: dbClusterIdentifier }).promise();
    const clusterStatus = clusterDetails.DBClusters[0].Status;
    // Toggle the cluster status
    if (clusterStatus === "available") {
        await rds.stopDBCluster({ DBClusterIdentifier: dbClusterIdentifier }).promise();
    }
    else if (clusterStatus === "stopped") {
        await rds.startDBCluster({ DBClusterIdentifier: dbClusterIdentifier }).promise();
    }
    return { statusCode: 200, body: "Operation completed successfully" };
};
