# Neo4j

## Installation
### Docker

`docker run -p 7474:7474 -p 7687:7687 -v $HOME/neo4j/wykop/data:/data neo4j`
This will run docker container pointing to wykop data folder. Now we can connect to db using browser: `http://localhost:7474`

- User: neo4j
- Password: Password1

## Cypher

- [Detail description of commands](https://neo4j.com/docs/developer-manual/current/cypher/clauses/)
- [Good explenation of MERGE and how to use it](https://www.graphgrid.com/using-neo4j-cypher-merge-effectively/)
- [MERGE with examples](https://graphaware.com/neo4j/2014/07/31/cypher-merge-explained.html)
- [Cypher cheat sheet](https://neo4j.com/docs/cypher-refcard/current/)
- [General documentation](https://neo4j.com/docs/developer-manual/current/introduction/graphdb-concepts/)

### Inserting data
```cypher
CREATE (:User {name:"User1", links:0})
CREATE (:User {name:"User2", links:0})
CREATE(:Link {id:"21245", url:"http://google.com", name:"Google", up: 0, down: 0, votes:0})
```
This creats nodes. String after colon define node type (User, Link). Nodes parameters are defined as key-value map. Those nodes are not unique. We can add multiple "User1" nodes.
### Match (select) command
```cypher
MATCH(u:User) RETURN *
MATCH(u:User) DETACH DELETE u
MATCH(u:User) WHERE u.name="User1" DETACH DELETE u
MATCH(u:User {name:"Tom"}) WHERE u.links < 4 RETURN u.name, u.links
```
Match have two parts. First responsivble for selecting nodes and second responsible for defining what to do with results. We can in example return found nodes or remove them.
When we know value of parameter we can put it in brackets. In other case we can use WHERE cause
```cypher
MATCH(u:User {name:"Tom", links:3}) RETURN u.name, u.links
MATCH(u:User {name:"Tom"}) WHERE u.links < 4 RETURN u.name, u.links
```
### Create relation between existing nodes
```cypher
MATCH (u:User {name:"User1"}), (l:Link {name:"Google"})
CREATE (u)-[:UP_VOTE]->(l)
```

**Warning!** Be aware about indexes and proformance of this query. (https://stackoverflow.com/questions/20456002/adding-relationship-to-existing-nodes-with-cypher)
It is possible to create indexes to avoid performance problems.
```cypher
CREATE INDEX ON :User(name)
CREATE INDEX ON :Link(name)
```
### Merge
Merge create or update existing node. This will create new user node.
```cypher
MERGE(u:User{name:"Tom", links:0})
```
It is importante to notice that it try to match both parameters, name and link. If I have already Tom node but with links equals 2 it will add new one with name Tom and links 0. 
To update params of exisitng node use `ON MATCH` and `ON CREATE` to set default value if node doesn't exists.
```cypher
MERGE(u:User{name:"Tom"})
ON CREATE SET u.links=0
ON MATCH SET u.links = u.links+1
```
We may also update properties when merging relation. In this case if nodes exists we will increase vote related valuse.
```cypher
MATCH (u:User {name:"Tom"}), (l:Link {name:"Google"})
MERGE (u)-[:UP_VOTE]->(l)
ON MATCH SET u.links=u.links+1
ON MATCH SET l.up=l.up+1
RETURN *
```
### Displaying relation label/type in results
```cypher
match (p:Page)<-[r]-(u:User) return p.id, u.name, TYPE(r)
```
