![Image](https://github.com/AnaPatSilva/Mexican_Restaurants-Postgraduate_workgroup-NEO4J/blob/main/meta.png)
# Mexican Restaurants (Postgraduation Workgroup)
# NEO4J

## My Intro
During the postgraduate course, we talked about database technologies and for non-relational databases, we had to do workgroup using Neo4J.

## Intro
_Which Mexican restaurant should you visit?_

This workgroup is based on the Yelp dataset, which contains data on various businesses, their categories, the reviews of the businesses and the users who wrote those reviews. The dataset also includes the relationships between users, making it possible to identify each user's friends.

Under the pretext of trying to find a good Mexican restaurant using the recommendations of well-known users, we're going to explore this dataset on Neo4j.

## Loading data into a free Neo4j instance
As we did in class, we use the free Neo4j instance in Neo4j Aura to load the Yelp data:

- Uploaded 8 CSV files representing the different nodes and edges of the graph ([Data](https://github.com/AnaPatSilva/Mexican_Restaurants-Postgraduation_Workgroup-NEO4J/tree/main/Data)).
- Used Neo4j Aura's Data Importer tool to load this data into the instance.
- Defined all the mapping between the different files and their columns to the nodes and edges, and their properties in Neo4j.

![Graph Database](https://github.com/AnaPatSilva/Mexican_Restaurants-Postgraduation_Workgroup-NEO4J/blob/main/Images/Graph%20Database.jpg)

## Questions to be answered
**1. The graph of all nodes of type Business or Category in which all Business nodes are part of the 'Mexican' category**

MATCH (n:Business)-[:IN]->(m:Category)

WHERE m.name = "Mexican"

RETURN n, m

![Question_1](https://github.com/AnaPatSilva/Mexican_Restaurants-Postgraduation_Workgroup-NEO4J/blob/main/Images/Question_1.jpg)

**2. The graph of all the nodes of any type directly connected to the restaurant called 'Los Comales'.**

MATCH (n:Review)-[:OF]->(m:Business)-[:IN]->(l:Category)

WHERE m.name ="Los Comales"

RETURN n, m, l

![Question_2](https://github.com/AnaPatSilva/Mexican_Restaurants-Postgraduation_Workgroup-NEO4J/blob/main/Images/Question_2.jpg)

**3. The same graph as the previous one, but this time including the nodes of type User that wrote the Reviews for this restaurant.**

MATCH (o:User)-[:WROTE]->(n:Review)-[:OF]->(m:Business)-[:IN]->(l:Category)

WHERE m.name ="Los Comales"

RETURN n, m, l, o

![Question_3](https://github.com/AnaPatSilva/Mexican_Restaurants-Postgraduation_Workgroup-NEO4J/blob/main/Images/Question_3.jpg)

**4. The graph of only those Users who have written a review of Los Comales. Are there any friendships between these people?**

MATCH (o:User)-[:WROTE]->(n:Review)-[:OF]->(m:Business)-[:IN]->(l:Category)

WHERE m.name ="Los Comales"

RETURN o

- Users who wrote a review of Los Comales (19 users)

![Question_4](https://github.com/AnaPatSilva/Mexican_Restaurants-Postgraduation_Workgroup-NEO4J/blob/main/Images/Question_4.jpg)

- Friendships between these people
There are no friendships

**5. How many stars did each of these people give Los Comales? Write a query that returns a table with two columns: user name and number of stars given.**

MATCH (o:User)-[:WROTE]->(n:Review)-[:OF]->(m:Business)

WHERE m.name ="Los Comales"

RETURN o.name AS nome_utilizador, n.stars AS numero_estrelas

ORDER BY n.stars DESC

![Question_5](https://github.com/AnaPatSilva/Mexican_Restaurants-Postgraduation_Workgroup-NEO4J/blob/main/Images/Question_5.jpg)

**6. What's the average rating for Los Comales?**

MATCH (o:User)-[:WROTE]->(n:Review)-[:OF]->(m:Business)

WHERE m.name ="Los Comales"

RETURN avg(n.stars) AS classificacao_media

![Question_6](https://github.com/AnaPatSilva/Mexican_Restaurants-Postgraduation_Workgroup-NEO4J/blob/main/Images/Question_6.jpg)

**7. Who are the friends of the users who wrote about Los Comales? Write a query that gives the graph of users who have written about Los Comales and their friends.**

MATCH (o:User)-[:WROTE]->(n:Review)-[:OF]->(m:Business {name:"Los Comales"})

OPTIONAL MATCH (p:User)-[:FRIEND_OF]-(o:User)

RETURN p,o

![Question_7](https://github.com/AnaPatSilva/Mexican_Restaurants-Postgraduation_Workgroup-NEO4J/blob/main/Images/Question_7.jpg)

**8. Of the Los Comales reviewers, which user has the most friends?**

MATCH (p:User)-[:FRIEND_OF]-(o:User)-[:WROTE]->(n:Review)-[:OF]->(m:Business)

WHERE m.name ="Los Comales"

RETURN o.name AS nome_utilizador, COUNT(p.user_id) AS numero_amigos

ORDER BY COUNT(p.user_id) DESC

![Question_8](https://github.com/AnaPatSilva/Mexican_Restaurants-Postgraduation_Workgroup-NEO4J/blob/main/Images/Question_8.jpg)

**9. What other businesses have the friends of the Los Comales reviewers written about?**

MATCH (l:Business)<-[:OF]-(k:Review)<-[:WROTE]-(p:User)-[:FRIEND_OF]->(o:User)-[:WROTE]->(n:Review)-[:OF]->(m:Business)

WHERE m.name = "Los Comales"

RETURN DISTINCT l.name AS Negocios

ORDER BY Negocios

![Question_9](https://github.com/AnaPatSilva/Mexican_Restaurants-Postgraduation_Workgroup-NEO4J/blob/main/Images/Question_9.jpg)

**10. How many of these also belong to the 'Mexican' category?**

MATCH (h:Category {name:"Mexican"})<-[:IN]-(l:Business)<-[:OF]-(k:Review)<-[:WROTE]-(p:User)-[:FRIEND_OF]->(o:User)-[:WROTE]->(n:Review)-[:OF]->(m:Business)

WHERE m.name = "Los Comales"

RETURN h,l

![Question_10](https://github.com/AnaPatSilva/Mexican_Restaurants-Postgraduation_Workgroup-NEO4J/blob/main/Images/Question_10.jpg)

**11. Which one has the highest average number of stars? Note that this average should be calculated taking into account all the Reviews and not just those written by friends of Los Comales reviewers.**

MATCH (x:Review)-[:OF]->(z:Business)-[:IN]->(h:Category {name:"Mexican"})

WHERE z.name = "Margaritas Mexican Restaurant" or z.name="Los Amigos Restaurant"

RETURN z.name AS Negocios, AVG(x.stars) AS media_estrelas

ORDER BY media_estrelas DESC

![Question_11](https://github.com/AnaPatSilva/Mexican_Restaurants-Postgraduation_Workgroup-NEO4J/blob/main/Images/Question_11.jpg)


# Conclusions
It was a funny way to work in a non-relational database.
The code was similar to SQL, but with the plus of the relationships and the direccion of them.
