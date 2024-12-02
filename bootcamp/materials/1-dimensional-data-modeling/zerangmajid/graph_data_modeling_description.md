# Graph Data Modeling Description

## Overview
**Graph data modeling** focuses on  **relationships** rather than  **entities**. It uses  **vertices** and  **edges** to represent interconnected data. 
Graph data modeling is a technique focused on capturing relationships between data entities. Unlike traditional models that prioritize tables and rows, graph models use nodes and edges, making them highly effective for use cases like social networks, recommendation systems, and relationship-driven datasets.

#### Why Graph Data Modeling?
- Captures complex relationships efficiently.
- Ideal for analyzing networks, social connections, and player interactions.

This document describes the **SQL scripts** used for **modeling graph data**. The repository is organized to showcase:
The database structure consists of **vertices** and **edges** that represent players, teams, games, and their relationships.

#### Additive vs. Non-Additive Dimensions
- **Additive Dimensions**: Data you can sum without overlap (e.g., Age groups in a population).
- **Non-Additive Dimensions**: Data prone to double counting (e.g., Active users across devices).

## Repository Structure

- **[1. graph_setup.sql](./1.graph_setup.sql)**: Sets up the graph database schema with vertices and edges.

- **[2. data_insertion.sql](./2.data_insertion.sql)**: Inserts sample data into the graph database.
- **[3. queries.sql](./3.queries.sql)**: Executes various queries to extract insights from the graph database.
- **[6. query_results.md](./6.query_results.md)**: Shows query results and explains their significance.



## Key Steps

### 1. Database Setup
The graph_setup.sql file defines the schema of the graph database, including:

Enumerated Types: Used to categorize vertices and edges.
Vertices Table: Stores nodes (e.g., players, teams, games) with their properties.
Edges Table: Stores relationships between nodes (e.g., "plays_in", "shares_team").[View Script](./1.graph_setup.sql)

### 2. Data Insertion
 The data_insertion.sql file populates the database with sample data for vertices and edges, representing:

Players and their properties (e.g., name, performance metrics).
Teams and games, along with their attributes.
Relationships such as which player played in which game or belongs to which team.
[View Script](./2.data_insertion.sql)


### 3. Querying the Database
The queries.sql file contains SQL queries to extract insights from the graph database. These include:

Player Performance: Analyze points scored, games played, and team affiliations.
Graph Relationships: Identify connections between players, teams, and games.
Custom Queries: Calculate metrics like games-to-points ratio and relationships between players.

## Example Query: Games Against Opponent
```sql
SELECT v.properties ->> 'player_name' AS player_name,
       e.object_identifier AS opponent_identifier,
       e.properties ->> 'subject_points' AS subject_points,
       e.properties ->> 'num_games' AS games_against_opponent
FROM vertices v
JOIN edges e ON v.identifier = e.subject_identifier
WHERE e.object_type = 'player'::vertex_type;
```
## Result Preview:
| Player Name    | Opponent ID | Subject Points | Games Against Opponent |
|----------------|-------------|----------------|-------------------------|
| Vince Carter   | 977         | 13             | 2                       |
| Vince Carter   | 1495        | 55             | 5                       |
| Dirk Nowitzki  | 708         | 29             | 1                       |
| Dirk Nowitzki  | 1495        | 4              | 1                       |

Key Insight:

Vince Carter has played 5 games against the player with ID 1495 and scored a total of 55 poin

[View Script](./3.queries.sql)


## Example Query: Player Statistics Aggregation
```sql
SELECT
    v.properties ->> 'player_name' AS player_name,
    AVG(CAST(v.properties ->> 'total_points' AS NUMERIC)) AS avg_points,
    COUNT(*) AS total_games
FROM vertices v
WHERE v.type = 'player'::vertex_type
GROUP BY v.properties ->> 'player_name'
ORDER BY avg_points DESC;
```
## Result Preview:
| Player Name    | Average Points | Total Games |
|----------------|----------------|-------------|
| Dirk Nowitzki	 | 25.7	          | 82          |
| Vince Carter   | 22.3           | 74          |

Key Insight:

Dirk Nowitzki has the highest average points per game among all players.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 

### Notes
- Remove duplicates using **ROW_NUMBER()** to maintain data integrity.
- Use **JSON properties** to allow flexibility in storing additional data.
- Index **fields** that are frequently queried for improved performance.


