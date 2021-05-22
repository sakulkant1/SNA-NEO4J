สำหรับ IMPORT และสร้าง Node ที่เชื่อม Relations

<<<<<<< CODE : Import Data From CSV >>>>>>>

load csv with headers from 
'file:///fraudTest.csv' as  row
merge (m:merchantnode {merchant_node :  row.merchant})
merge (a:account_holder_node {fullname_node : row.first +" "+ row.last}  )
merge (t:transactionnode {Running_Number_node : row.Running_Number , trans_date_trans_time_node :  row.trans_date_trans_time})

merge(a)-[h:has_transaction]->(t)
merge(t)-[p:purchased]->(m);

Libary สำหรับใช้คำสั่ง Betweenness เพื่อนำมาวิเคราะห์ข้อมูล
<<<<<<<< Betweenness : fullname_node >>>>>>>>>>>

CALL gds.betweenness.stream({
 nodeProjection : '*',
 relationshipProjection :{
    similar: {
        type : 'has_transaction',
        orientation : 'UNDIRECTED'
    }
  }
})
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).fullname_node as ID,score
ORDER BY score DESC LIMIT 5


<<<<<<<< Betweenness : merchant_node >>>>>>>>>>>

CALL gds.betweenness.stream({
nodeProjection : '*',
 relationshipProjection :{
    similar: {
        type : 'purchased',
        orientation : 'UNDIRECTED'
    }
  }
})
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).merchant_node as ID,score
ORDER BY score DESC LIMIT 5

Libary สำหรับใช้คำสั่ง Drgree เพื่อนำมาวิเคราะห์ข้อมูล
<<<<<<<< Drgree : account_holder_node >>>>>>>>>>>

MATCH (a:account_holder_node)
RETURN a.fullname_node as name , size((a)-[]-()) as drgree ORDER BY drgree  DESC
LIMIT 5


<<<<<<<< Drgree : merchantnode >>>>>>>>>>>

MATCH (m:merchantnode)
RETURN m.merchant_node as name , size((m)-[]-()) as drgree ORDER BY drgree  DESC
LIMIT 5




MATCH (a:account_holder_node  )-[h:has_transaction]-(t:transactionnode)
,(t:transactionnode)-[p:purchased]->(m:merchantnode)
where a.fullname_node = 'Mary Williams'
RETURN  a,h,t,p,m



MATCH (m:merchantnode)<-[p:purchased]-(t:transactionnode)
where size((m)-[]-()) = 18
RETURN   m,p,t


MATCH (n
)
DETACH DELETE (n)

