# Graph Agil Multidimensionnal Model (GAMM)

The GAMM is a flexible approach to schema and data evolution in data warehouses. The model allows designers to integrate new data sources and accommodate new user requirements to enrich the analytical capabilities of the data warehouse. It is an approach based on a multi-version scalable schema model and a data warehouse stored in a unique global graph database. Each schema version (SV) is valid for a period of time (T) characterised by a Starting_Time (ST) and an Ending_Time (ET), and it corresponds to a data instance (DInst) extracted from the graph-based data warehouse. 

According to the GAMM approach, we have generated 3 chronological versions using the SSB data as shown below. As a reminder, the Star Schema Benchmark, or SSB, was designed to evaluate the performance of database systems for star schema data warehouse queries. The SSB schema is based on the [TPC-H benchmark] (http://www.tpc.org/tpch/), but in a modified form with LINEORDER as a central fact table and dimension tables for customer, part, supplier, and date. The queries are also based on the TPC-H queries, but the number of queries is reduced to 13 and have been chosen to cover as much of the SSB as possible, so that potential users can derive a performance evaluation of the weighted subset they expect to use in practice. They are divided into 4 subgroups according to the difficulty and the number of domensions involved. The idea is that the total number of fact table rows retrieved will be determined by the selectivity (i.e., total Filter Factor FF) of restrictions on dimensions. More details in [Paper Download](https://www.cs.umb.edu/~poneil/StarSchemaB.PDF)

![Cover](https://github.com/Redwass/GAMM/blob/main/figures/gamm_schema_evolution.jpg)

As the SSB data spans from 1992 to 1998, the versioning was set as follows :

(1) A 1st schema for 1992 and 1993 data composed of a fact Sales analyzed according to the Part and Customer dimensions. The fact Sales is indicated by the measures Sales_Amount, Discount and Supplycost. The dimension Part is described by the attributes P_Name and Size and a hierarchical levels Category, Mfgr, Brand and Type. The dimension Customer is described by the attributes C_Name, C_Address and C_Phone, and a hierarchical levels C_City, C_Nation, C_Region and Segment. Note that for further study, we ourselves created these levels that were attributes in the SSB original dataset. This version is valid during the period ????1 =[01/01/2019,31/12/2019] 

(2) A 2nd schema version for data from 1994 to 1996 characterised by he addition of a new Supplier dimension described by the attributes S_Name, S_Address and S_Phone, and a hierarchical levels S_City, S_Nation and S_Region. This version is valid during the period ????2 =[01/01/2020,31/12/2020]

(3) A 3rd version of the schema for 1997 and 1998 data characterised by the removal of the Customer dimension and the addition of a Quantity measure. This version is valid during the period ????3 = [01/01/2021,31/12/2021]

It should be noted that the validity period of the versions may correspond to the effective validity period of the data as well as being different for dealing with earlier data as in this case.  

We were able to apply the 13 queries proposed on SSB which we took up in CRL either explicitly to query a particular version or implicitly to query all versions according to the existing schema. Some queries were readjusted to match the time intervals established during the versioning. Note that some queries has been adjusted to match the different schema versions.

## 1. Platform, data and installation. 

To install the GAMM with SSB data, use the graph database Neo4j version 4.x.x or higher. The data to be downloaded from [Download](https://github.com/Redwass/GAMM/tree/main/data) is in the CSV's files. The configuration and installation commands on Windows and Linux platforms are as follows:

### 1.1 Neo4j configuration

This configuration is for 16,0 Go of RAM

#### Java Heap Size: 

dbms.memory.heap.initial_size=4096m

dbms.memory.heap.max_size=4096m

dbms.memory.pagecache.size=9216m

### 1.2 Create data for database

#### For windows 
```cmd
neo4j-admin.bat import --database=gamm --nodes=lineorder=data\lineorder_92_93-header.csv,data\lineorder_92_93.csv --nodes=lineorder=data\lineorder_94_96-header.csv,data\lineorder_94_96.csv --nodes=lineorder=data\lineorder_97_98-header.csv,data\lineorder_97_98.csv --nodes=p_brand=data\p_brand_92_98-header.csv,data\p_brand_92_98.csv --nodes=p_category=data\p_category_92_98-header.csv,data\p_category_92_98.csv --nodes=p_mfgr=data\p_mfgr_92_98-header.csv,data\p_mfgr_92_98.csv --nodes=p_type=data\p_type_92_98-header.csv,data\p_type_92_98.csv --nodes=part=data\part_92_98-header.csv,data\part_92_98.csv --nodes=part_name=data\part_name_92_98-header.csv,data\part_name_92_98.csv --nodes=part_size=data\part_size_92_98-header.csv,data\part_size_92_98.csv --nodes=c_city=data\c_city_92_96-header.csv,data\c_city_92_96.csv --nodes=c_mktsegment=data\c_mktsegment_92_96-header.csv,data\c_mktsegment_92_96.csv --nodes=c_nation=data\c_nation_92_96-header.csv,data\c_nation_92_96.csv --nodes=c_region=data\c_region_92_96-header.csv,data\c_region_92_96.csv --nodes=customer=data\customer_92_96-header.csv,data\customer_92_96.csv --nodes=customer_address=data\customer_address_92_96-header.csv,data\customer_address_92_96.csv --nodes=customer_name=data\customer_name_92_96-header.csv,data\customer_name_92_96.csv --nodes=customer_phone=data\customer_phone_92_96-header.csv,data\customer_phone_92_96.csv --nodes=s_city=data\s_city_94_98-header.csv,data\s_city_94_98.csv --nodes=s_nation=data\s_nation_94_98-header.csv,data\s_nation_94_98.csv --nodes=s_region=data\s_region_94_98-header.csv,data\s_region_94_98.csv --nodes=supplier=data\supplier_94_98-header.csv,data\supplier_94_98.csv --nodes=supplier_address=data\supplier_address_94_98-header.csv,data\supplier_address_94_98.csv --nodes=supplier_name=data\supplier_name_94_98-header.csv,data\supplier_name_94_98.csv --nodes=supplier_phone=data\supplier_phone_94_98-header.csv,data\supplier_phone_94_98.csv --nodes=date=data\date-header.csv,data\date.csv --relationships=order_part=data\order_part_92_98-header.csv,data\order_part_92_98.csv --relationships=order_customer=data\order_customer_92_96-header.csv,data\order_customer_92_96.csv --relationships=order_supplier=data\order_supplier_94_98-header.csv,data\order_supplier_94_98.csv --relationships=order_date=data\order_date-header.csv,data\order_date.csv --relationships=part_brand=data\part_to_brand_92_98-header.csv,data\part_to_brand_92_98.csv --relationships=part_category=data\part_to_category_92_98-header.csv,data\part_to_category_92_98.csv --relationships=part_mfgr=data\part_to_mfgr_92_98-header.csv,data\part_to_mfgr_92_98.csv --relationships=part_name=data\part_to_name_92_98-header.csv,data\part_to_name_92_98.csv --relationships=part_size=data\part_to_size_92_98-header.csv,data\part_to_size_92_98.csv --relationships=part_type=data\part_to_type_92_98-header.csv,data\part_to_type_92_98.csv --relationships=customer_address=data\customer_to_address_92_96-header.csv,data\customer_to_address_92_96.csv --relationships=customer_city=data\customer_to_city_92_96-header.csv,data\customer_to_city_92_96.csv --relationships=customer_mktsegment=data\customer_to_mktsegment_92_96-header.csv,data\customer_to_mktsegment_92_96.csv --relationships=customer_name=data\customer_to_name_92_96-header.csv,data\customer_to_name_92_96.csv --relationships=customer_nation=data\customer_to_nation_92_96-header.csv,data\customer_to_nation_92_96.csv --relationships=customer_phone=data\customer_to_phone_92_96-header.csv,data\customer_to_phone_92_96.csv --relationships=customer_region=data\customer_to_region_92_96-header.csv,data\customer_to_region_92_96.csv --relationships=supplier_address=data\supplier_to_address_94_98-header.csv,data\supplier_to_address_94_98.csv --relationships=supplier_city=data\supplier_to_city_94_98-header.csv,data\supplier_to_city_94_98.csv --relationships=supplier_name=data\supplier_to_name_94_98-header.csv,data\supplier_to_name_94_98.csv --relationships=supplier_nation=data\supplier_to_nation_94_98-header.csv,data\supplier_to_nation_94_98.csv --relationships=supplier_phone=data\supplier_to_phone_94_98-header.csv,data\supplier_to_phone_94_98.csv --relationships=supplier_region=data\supplier_to_region_94_98-header.csv,data\supplier_to_region_94_98.csv
```

#### For Linux 
```cmd
neo4j-admin import --database=gamm --nodes=lineorder=data\lineorder_92_93-header.csv,data\lineorder_92_93.csv --nodes=lineorder=data\lineorder_94_96-header.csv,data\lineorder_94_96.csv --nodes=lineorder=data\lineorder_97_98-header.csv,data\lineorder_97_98.csv --nodes=p_brand=data\p_brand_92_98-header.csv,data\p_brand_92_98.csv --nodes=p_category=data\p_category_92_98-header.csv,data\p_category_92_98.csv --nodes=p_mfgr=data\p_mfgr_92_98-header.csv,data\p_mfgr_92_98.csv --nodes=p_type=data\p_type_92_98-header.csv,data\p_type_92_98.csv --nodes=part=data\part_92_98-header.csv,data\part_92_98.csv --nodes=part_name=data\part_name_92_98-header.csv,data\part_name_92_98.csv --nodes=part_size=data\part_size_92_98-header.csv,data\part_size_92_98.csv --nodes=c_city=data\c_city_92_96-header.csv,data\c_city_92_96.csv --nodes=c_mktsegment=data\c_mktsegment_92_96-header.csv,data\c_mktsegment_92_96.csv --nodes=c_nation=data\c_nation_92_96-header.csv,data\c_nation_92_96.csv --nodes=c_region=data\c_region_92_96-header.csv,data\c_region_92_96.csv --nodes=customer=data\customer_92_96-header.csv,data\customer_92_96.csv --nodes=customer_address=data\customer_address_92_96-header.csv,data\customer_address_92_96.csv --nodes=customer_name=data\customer_name_92_96-header.csv,data\customer_name_92_96.csv --nodes=customer_phone=data\customer_phone_92_96-header.csv,data\customer_phone_92_96.csv --nodes=s_city=data\s_city_94_98-header.csv,data\s_city_94_98.csv --nodes=s_nation=data\s_nation_94_98-header.csv,data\s_nation_94_98.csv --nodes=s_region=data\s_region_94_98-header.csv,data\s_region_94_98.csv --nodes=supplier=data\supplier_94_98-header.csv,data\supplier_94_98.csv --nodes=supplier_address=data\supplier_address_94_98-header.csv,data\supplier_address_94_98.csv --nodes=supplier_name=data\supplier_name_94_98-header.csv,data\supplier_name_94_98.csv --nodes=supplier_phone=data\supplier_phone_94_98-header.csv,data\supplier_phone_94_98.csv --nodes=date=data\date-header.csv,data\date.csv --relationships=order_part=data\order_part_92_98-header.csv,data\order_part_92_98.csv --relationships=order_customer=data\order_customer_92_96-header.csv,data\order_customer_92_96.csv --relationships=order_supplier=data\order_supplier_94_98-header.csv,data\order_supplier_94_98.csv --relationships=order_date=data\order_date-header.csv,data\order_date.csv --relationships=part_brand=data\part_to_brand_92_98-header.csv,data\part_to_brand_92_98.csv --relationships=part_category=data\part_to_category_92_98-header.csv,data\part_to_category_92_98.csv --relationships=part_mfgr=data\part_to_mfgr_92_98-header.csv,data\part_to_mfgr_92_98.csv --relationships=part_name=data\part_to_name_92_98-header.csv,data\part_to_name_92_98.csv --relationships=part_size=data\part_to_size_92_98-header.csv,data\part_to_size_92_98.csv --relationships=part_type=data\part_to_type_92_98-header.csv,data\part_to_type_92_98.csv --relationships=customer_address=data\customer_to_address_92_96-header.csv,data\customer_to_address_92_96.csv --relationships=customer_city=data\customer_to_city_92_96-header.csv,data\customer_to_city_92_96.csv --relationships=customer_mktsegment=data\customer_to_mktsegment_92_96-header.csv,data\customer_to_mktsegment_92_96.csv --relationships=customer_name=data\customer_to_name_92_96-header.csv,data\customer_to_name_92_96.csv --relationships=customer_nation=data\customer_to_nation_92_96-header.csv,data\customer_to_nation_92_96.csv --relationships=customer_phone=data\customer_to_phone_92_96-header.csv,data\customer_to_phone_92_96.csv --relationships=customer_region=data\customer_to_region_92_96-header.csv,data\customer_to_region_92_96.csv --relationships=supplier_address=data\supplier_to_address_94_98-header.csv,data\supplier_to_address_94_98.csv --relationships=supplier_city=data\supplier_to_city_94_98-header.csv,data\supplier_to_city_94_98.csv --relationships=supplier_name=data\supplier_to_name_94_98-header.csv,data\supplier_to_name_94_98.csv --relationships=supplier_nation=data\supplier_to_nation_94_98-header.csv,data\supplier_to_nation_94_98.csv --relationships=supplier_phone=data\supplier_to_phone_94_98-header.csv,data\supplier_to_phone_94_98.csv --relationships=supplier_region=data\supplier_to_region_94_98-header.csv,data\supplier_to_region_94_98.csv

```

### 1.3 Create database
After the database is set up using the neo4j-admin command, it must be created manually on :

#### Command line
CREATE DATABASE gammssb (on system database)

#### Neo4j desktop 
on the <<+Create database>> tab with the name gammssb.

### 1.4 Create indexes
This is not required, but you can create the following indexes to optimise query execution :

create index index_part_category for (p:part) on (p.P_CATEGORY);

create index index_part_brand for (p:part) on (p.P_BRAND);

create index idxex_customer_region for (c:customer) on (c.C_REGION);

create index idxex_customer_nation for (c:customer) on (c.C_NATION);

create index idxex_customer_city for (c:customer) on (c.C_CITY);

## 2. Queries

Here are the 13 SSB queries using the Cypher Language Request (CLR) and some extra queries to explain explicit and implicit requests in GAMM. They can be applied on Cypher-shell or in Neo4j Browser.

GAMM offers a great flexibility in the elaboration of explicit and implicit queries which we detail through the following examples. The queries are written in Cypher Language Request CLR specific to the Neo4j graph database :

(1) Explicit requests. Allow to extract data from a specific version or set of versions based on the time parameter TT using the clause **where ???????????? <=TT<????????????** with ???????? =[???????????? ,???????????? ] is the period of validity of version ????????. 

Example 1 :
```cypher
match (d:date)<-[:order_date]-(l:lineorder)-[r:order_part]->(p:part) 
where ????????2 <= l.TT < ????????2 
return d.d_year, count(distinct(p.partkey)), sum(l.lo_revenue) 
order by d.d_year
```
Note that in the CRL, the clause : **return ???????????????????? 1,.., ???????????????????? N , aggregate_function(attribute)** allows for grouping aggregation by ???????????????????? 1... ???????????????????? N.

(2) Implicit requests: Allows the browsing of all instances related to the entities specified in the query without version restriction. This feature, which is due to the ability of graph databases to traverse instances through the relationships between entities, offers a big advantage in formulating cross-queries using the same simple queries whatever the number of versions. 

Example 2 :
```cypher
match (d:date)<-[:order_date]-(l:lineorder)-[r:order_part]->(p:part) 
return d.d_year, count(distinct(p.partkey)), sum(l.lo_revenue) 
order by d.d_year
```
The results of the queries presented in the examples are shown as follows :

![Cover](https://github.com/Redwass/GAMM/blob/main/figures/queries_results.JPG)

Explicit query in example 1 displays the slaes_amount per year and the number of pieces. According to TT parameter, the result is obtained only on version 2 although the dimension part has instances in all 3 versions.

In the other side, implicit query in example 2 witch is same like query 1 with out TT parameter show the result in all 3 versions as the dimension  part exists in all these 3 versions of the scheme.

We present below an implicit and explicit version of each of the 13 proposed queries on SSB set up in CLR. Some queries have been readjusted to match the time intervals established during versioning or the schema of version. For example, the first query in SSB was adjusted by changing the D_YEAR attribute to 1997 instead of 1994 in SSB to match the versioning we established because the Quantity measure was created only from the 3rd version of the schema and using data from the years 1997 and 1998.

##### Q1.1 

###### Implicit version (The query will extract data from version 3 since the QUANTITY measure was only added in version 3 ) 

```cypher
profile optional match (d:date{D_YEAR:1997})<-[r:order_date]-(l:lineorder)
where 1<= l.LO_DISCOUNT <=3
and l.LO_QUANTITY < 25
return sum(l.LO_REVENUE);
```

###### Explicit version (version 3)  
```cypher
profile optional match (d:date{D_YEAR:1997})<-[r:order_date]-(l:lineorder)
where date({year:2021,month:01}) <= l.TRANSACTION_TIME
and 1<= l.LO_DISCOUNT <=3
and l.LO_QUANTITY < 25
return sum(l.LO_REVENUE);
```

##### Q1.2

###### Implicit version (The query will extract data from version 3 ) 

```cypher
profile optional match (d:date{D_YEARMONTHNUM:199701})<-[:order_date]-(l:lineorder)
where 4 <= l.LO_DISCOUNT <=6
and 26 <= l.LO_QUANTITY <= 35
return sum(l.LO_REVENUE);
```

###### Explicit version (version 3)

```cypher
profile optional match (d:date{D_YEARMONTHNUM:199701})<-[:order_date]-(l:lineorder)
where date({year:2021,month:01}) <= l.TRANSACTION_TIME <= date({year:2021,month:12})
and 4 <= l.LO_DISCOUNT <=6
and 26 <= l.LO_QUANTITY <= 35
return sum(l.LO_REVENUE);
```

##### Q1.3

###### Implicit version (The query will extract data from version 3 ) 

```cypher
profile optional match (d:date{D_YEAR:1997, D_WEEKNUMINYEAR:6})<-[r:order_date]-(l:lineorder)
where 5<= l.LO_DISCOUNT <=7
and 26<= l.LO_QUANTITY <= 35
return sum(l.LO_REVENUE);
```

###### Explicit version (version 3)

```cypher
profile optional match (d:date{D_YEAR:1997, D_WEEKNUMINYEAR:6})<-[r:order_date]-(l:lineorder)
where date({year:2021,month:01}) <= l.TRANSACTION_TIME <= date({year:2021,month:12})
and 5<= l.LO_DISCOUNT <=7
and 26<= l.LO_QUANTITY <= 35
return sum(l.LO_REVENUE);
```

##### Q2.1

###### Implicit version (The query will extract data from version 2 and 3 ) 

```cypher
profile optional match (pb:p_brand)<-[:part_brand]-(p:part)<-[:order_part]-(l:lineorder)-[:order_supplier]->(s:supplier)-[:supplier_region]->(sr:s_region),(d:date)<-[:order_date]-(l)
where sr.S_REGION = "AMERICA"
return sum(l.LO_REVENUE),d.D_YEAR,pb.P_BRAND
ORDER BY d.D_YEAR, pb.P_BRAND
```

###### Explicit version (version 2)

```cypher
profile optional match (pb:p_brand)<-[:part_brand]-(p:part)<-[:order_part]-(l:lineorder)-[:order_supplier]->(s:supplier)-[:supplier_region]->(sr:s_region),(d:date)<-[:order_date]-(l)
where date({year:2020,month:01}) <= l.TRANSACTION_TIME < date({year:2021,month:1})
and sr.S_REGION = "AMERICA"
return sum(l.LO_REVENUE),d.D_YEAR,pb.P_BRAND
ORDER BY d.D_YEAR, pb.P_BRAND
```

##### Q2.2
###### Implicit version (The query will extract data from version 2 and 3 ) 

```cypher
profile optional match (pb:p_brand)<-[:part_brand]-(p:part)<-[:order_part]-(l:lineorder)-[:order_supplier]->(s:supplier)-[:supplier_region]->(sr:s_region),(d:date)<-[:order_date]-(l)
where (pb.P_BRAND >= "MFGR#2221" and pb.P_BRAND <= "MFGR#2228")
and sr.S_REGION = "ASIA"
return sum(l.LO_REVENUE),d.D_YEAR,pb.P_BRAND
ORDER BY d.D_YEAR, pb.P_BRAND
```

###### Explicit version (version 3)

```cypher
profile optional match (pb:p_brand)<-[:part_brand]-(p:part)<-[:order_part]-(l:lineorder)-[:order_supplier]->(s:supplier)-[:supplier_region]->(sr:s_region),(d:date)<-[:order_date]-(l)
where date({year:2021,month:01}) <= l.TRANSACTION_TIME < date({year:2022,month:01})
and (pb.P_BRAND >= "MFGR#2221" and pb.P_BRAND <= "MFGR#2228")
and sr.S_REGION = "ASIA"
return sum(l.LO_REVENUE),d.D_YEAR,pb.P_BRAND
ORDER BY d.D_YEAR, pb.P_BRAND
```

##### Q2.3
###### Implicit version (The query will extract data from version 2 and 3 ) 

```cypher
profile optional match (pb:p_brand)<-[:part_brand]-(p:part)<-[:order_part]-(l:lineorder)-[:order_supplier]->(s:supplier)-[:supplier_region]->(sr:s_region),(d:date)<-[:order_date]-(l)
where pb.P_BRAND = "MFGR#2239" 
and sr.S_REGION = "EUROPE"
return sum(l.LO_REVENUE),d.D_YEAR,pb.P_BRAND
ORDER BY d.D_YEAR, pb.P_BRAND
```

###### Explicit version (version 2)

```cypher
profile optional match (pb:p_brand)<-[:part_brand]-(p:part)<-[:order_part]-(l:lineorder)-[:order_supplier]->(s:supplier)-[:supplier_region]->(sr:s_region),(d:date)<-[:order_date]-(l)
where date({year:2020,month:01}) <= l.TRANSACTION_TIME < date({year:2021,month:01})
and pb.P_BRAND = "MFGR#2239" 
and sr.S_REGION = "EUROPE"
return sum(l.LO_REVENUE),d.D_YEAR,pb.P_BRAND
ORDER BY d.D_YEAR, pb.P_BRAND
```

##### Q3.1

###### Implicit version (The query will extract data from version 1 and 2) 

```cypher
profile optional match (cr:c_region)<-[:customer_region]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_date]->(d:date),(sr:s_region)<-[:supplier_region]-(s:supplier)<-[:order_supplier]-(l)
,(cn:c_nation)<-[:customer_nation]-(c),(sn:s_nation)<-[:supplier_nation]-(s)
where 1994<= d.D_YEAR <=1995
and cr.C_REGION = "ASIA"
and sr.S_REGION = "ASIA" 
return cn.C_NATION, sn.S_NATION, d.D_YEAR, sum(l.LO_REVENUE) as revenu
ORDER BY d.D_YEAR ASC, revenu DESC
```

###### Explicit version (version 2)

```cypher
profile optional match (cr:c_region)<-[:customer_region]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_date]->(d:date),(sr:s_region)<-[:supplier_region]-(s:supplier)<-[:order_supplier]-(l),
(cn:c_nation)<-[:customer_nation]-(c),(sn:s_nation)<-[:supplier_nation]-(s)
where date({year:2021,month:01}) <= l.TRANSACTION_TIME < date({year:2022,month:01})
and 1994<= d.D_YEAR <=1995
and cr.C_REGION starts with "ASIA"
and sr.S_REGION = "ASIA" 
return cn.C_NATION, sn.S_NATION, d.D_YEAR, sum(l.LO_REVENUE) as revenu
ORDER BY d.D_YEAR ASC, revenu DESC
```

##### Q3.2

###### Implicit version (The query will extract data from version 1 and 2) 

```cypher
optional match (cn:c_nation)<-[:customer_nation]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_date]->(d:date),(sn:s_nation)<-[:supplier_nation]-(s:supplier)<-[:order_supplier]-(l),
(cc:c_city)<-[:customer_city]-(c),(sc:s_city)<-[:supplier_city]-(s)
where 1994<= d.D_YEAR <=1995
and cn.C_NATION = "UNITED STATES"
and sn.S_NATION = "UNITED STATES" 
return cc.C_CITY, sc.S_CITY, d.D_YEAR, sum(l.LO_REVENUE) as revenu
ORDER BY d.D_YEAR ASC, revenu DESC
```

###### Explicit version (version 2)

```cypher
optional match (cn:c_nation)<-[:customer_nation]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_date]->(d:date),(sn:s_nation)<-[:supplier_nation]-(s:supplier)<-[:order_supplier]-(l),
(cc:c_city)<-[:customer_city]-(c),(sc:s_city)<-[:supplier_city]-(s)
where date({year:2019,month:01}) <= l.TRANSACTION_TIME < date({year:2022,month:01})
and 1994<= d.D_YEAR <=1995
and cn.C_NATION = "UNITED STATES"
and sn.S_NATION = "UNITED STATES" 
return cc.C_CITY, sc.S_CITY, d.D_YEAR, sum(l.LO_REVENUE) as revenu
ORDER BY d.D_YEAR ASC, revenu DESC

```

##### Q3.3

###### Implicit version (The query will extract data from version 1 and 2) 

```cypher
optional match (cc:c_city)<-[:customer_city]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_date]->(d:date),(sc:s_city)<-[:supplier_city]-(s:supplier)<-[:order_supplier]-(l)
where 1994<= d.D_YEAR <=1995
and (cc.C_CITY = "UNITED KI1" or cc.C_CITY = "UNITED KI5") 
and (sc.S_CITY = "UNITED KI1" or sc.S_CITY = "UNITED KI5") 
return cc.C_CITY, sc.S_CITY, d.D_YEAR, sum(l.LO_REVENUE) as revenu
ORDER BY d.D_YEAR ASC, revenu DESC
```

###### Explicit version (version 2)

```cypher
match (cc:c_city)<-[:customer_city]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_date]->(d:date),(sc:s_city)<-[:supplier_city]-(s:supplier)<-[:order_supplier]-(l)
where date({year:2020,month:01}) <= l.TRANSACTION_TIME < date({year:2021,month:01})
and 1994<= d.D_YEAR <=1995
and (cc.C_CITY = "UNITED KI1" or cc.C_CITY = "UNITED KI5") 
and (sc.S_CITY = "UNITED KI1" or sc.S_CITY = "UNITED KI5") 
return cc.C_CITY, sc.S_CITY, d.D_YEAR, sum(l.LO_REVENUE) as revenu
ORDER BY d.D_YEAR ASC, revenu DESC
```

##### Q3.4

###### Implicit version (The query will extract data from version 1 and 2) 

```cypher
optional match (cc:c_city)<-[:customer_city]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_date]->(d:date),(sc:s_city)<-[:supplier_city]-(s:supplier)<-[:order_supplier]-(l)
where (cc.C_CITY = "UNITED KI1" or cc.C_CITY = "UNITED KI5") 
and (sc.S_CITY = "UNITED KI1" or sc.S_CITY = "UNITED KI5") 
and d.D_YEARMONTH = "Dec1995" 
return cc.C_CITY, sc.S_CITY, d.D_YEAR, sum(l.LO_REVENUE) as revenu
ORDER BY d.D_YEAR ASC, revenu DESC
```

###### Explicit version (version 2)

```cypher
optional match (cc:c_city)<-[:customer_city]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_date]->(d:date),(sc:s_city)<-[:supplier_city]-(s:supplier)<-[:order_supplier]-(l)
where date({year:2020,month:01}) <= l.TRANSACTION_TIME <= date({year:2021,month:01})
and (cc.C_CITY = "UNITED KI1" or cc.C_CITY = "UNITED KI5") 
and (sc.S_CITY = "UNITED KI1" or sc.S_CITY = "UNITED KI5") 
and d.D_YEARMONTH = "Dec1995" 
return cc.C_CITY, sc.S_CITY, d.D_YEAR, sum(l.LO_REVENUE) as revenu
ORDER BY d.D_YEAR ASC, revenu DESC
```

##### Q4.1

###### Implicit version (The query will extract data from version 2 ) 

```cypher
profile optional match (cr:c_region)<-[:customer_region]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_part]->(p:part)-[:part_mfgr]->(pm:p_mfgr),
(sr:s_region)<-[:supplier_region]-(s:supplier)<-[:order_supplier]-(l)-[:order_date]->(d:date),(cn:c_nation)<-[:customer_nation]-(c)
where cr.C_REGION = "AMERICA" 
and (pm.P_MFGR = "MFGR#1" or pm.P_MFGR = "MFGR#2")
and sr.S_REGION = "AMERICA"
return d.D_YEAR, cn.C_NATION, (sum(l.LO_REVENUE) - sum(l.LO_SUPPLYCOST)) as supplycost
ORDER BY d.D_YEAR, cn.C_NATION;
```

###### Explicit version (version 2)

```cypher
profile optional match (cr:c_region)<-[:customer_region]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_part]->(p:part)-[:part_mfgr]->(pm:p_mfgr),
(sr:s_region)<-[:supplier_region]-(s:supplier)<-[:order_supplier]-(l)-[:order_date]->(d:date),(cn:c_nation)<-[:customer_nation]-(c)
where date({year:2020,month:01}) <= l.TRANSACTION_TIME < date({year:2021,month:01})
and cr.C_REGION = "AMERICA" 
and (pm.P_MFGR = "MFGR#1" or pm.P_MFGR = "MFGR#2")
and sr.S_REGION = "AMERICA"
return d.D_YEAR, cn.C_NATION, (sum(l.LO_REVENUE) - sum(l.LO_SUPPLYCOST)) as supplycost
ORDER BY d.D_YEAR, cn.C_NATION;
```

##### Q4.2

###### Implicit version (The query will extract data from version 2 ) 

```cypher
profile optional match (cr:c_region)<-[:customer_region]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_part]->(p:part)-[:part_mfgr]->(pm:p_mfgr),
(sr:s_region)<-[:supplier_region]-(s:supplier)<-[:order_supplier]-(l)-[:order_date]->(d:date),
(cn:c_nation)<-[:customer_nation]-(c),(p)-[:part_category]->(pc:p_category)
where cr.C_REGION = "AMERICA" 
and (d.D_YEAR = 1994 or d.D_YEAR = 1995)
and (pm.P_MFGR = "MFGR#1" or pm.P_MFGR = "MFGR#2")
and sr.S_REGION = "AMERICA"
return d.D_YEAR, cn.C_NATION, pc.P_CATEGORY, (sum(l.LO_REVENUE) - sum(l.LO_SUPPLYCOST)) as supplycost
ORDER BY d.D_YEAR, cn.C_NATION, pc.P_CATEGORY;
```

###### Explicit version (version 2)

```cypher
optional match (cr:c_region)<-[:customer_region]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_part]->(p:part)-[:part_mfgr]->(pm:p_mfgr),
(sr:s_region)<-[:supplier_region]-(s:supplier)<-[:order_supplier]-(l)-[:order_date]->(d:date),
(cn:c_nation)<-[:customer_nation]-(c),(p)-[:part_category]->(pc:p_category)
where date({year:2020,month:01}) <= l.TRANSACTION_TIME < date({year:2020,month:01})
and cr.C_REGION = "AMERICA" 
and (d.D_YEAR = 1994 or d.D_YEAR = 1995)
and (pm.P_MFGR = "MFGR#1" or pm.P_MFGR = "MFGR#2")
and sr.S_REGION = "AMERICA"
return d.D_YEAR, cn.C_NATION, pc.P_CATEGORY, (sum(l.LO_REVENUE) - sum(l.LO_SUPPLYCOST)) as supplycost
ORDER BY d.D_YEAR, cn.C_NATION, pc.P_CATEGORY;
```

##### Q4.3

###### Implicit version (The query will extract data from version 2 ) 

```cypher
optional match (cr:c_region)<-[:customer_region]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_part]->(p:part)-[:part_category]->(pc:p_category),
(sn:s_nation)<-[:supplier_nation]-(s:supplier)<-[:order_supplier]-(l)-[:order_date]->(d:date),(p)-[:part_brand]->(pb:p_brand),(s)-[:supplier_city]->(sc:s_city)
where date({year:2019,month:01}) <= l.TRANSACTION_TIME <= date({year:2021,month:12})
and cr.C_REGION = "AMERICA"
and pc.P_CATEGORY = "MFGR#14"
and sn.S_NATION = "UNITED STATES" 
return d.D_YEAR, sc.S_CITY, pb.P_BRAND,(sum(l.LO_REVENUE) - sum(l.LO_SUPPLYCOST)) as supplycost
ORDER BY d.D_YEAR, sc.S_CITY, pb.P_BRAND;
```

###### Explicit version (version 2)

```cypher
optional match (cr:c_region)<-[:customer_region]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_part]->(p:part)-[:part_category]->(pc:p_category),
(sn:s_nation)<-[:supplier_nation]-(s:supplier)<-[:order_supplier]-(l)-[:order_date]->(d:date),(p)-[:part_brand]->(pb:p_brand),(s)-[:supplier_city]->(sc:s_city)
where date({year:2020,month:01}) <= l.TRANSACTION_TIME < date({year:2021,month:01})
and cr.C_REGION = "AMERICA"
and pc.P_BRAND = "MFGR#14"
and sn.S_NATION = "UNITED STATES" 
return d.D_YEAR, sc.S_CITY, pb.P_BRAND,(sum(l.LO_REVENUE) - sum(l.LO_SUPPLYCOST)) as supplycost
ORDER BY d.D_YEAR, sc.S_CITY, pb.P_BRAND;
```
