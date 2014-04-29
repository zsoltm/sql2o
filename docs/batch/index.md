---
title: Running queries in a batch
layout: docs
topmenu: docs
leftmenu: batch
---

If you need to run an UPDATE, INSERT or DELETE query multiple times with different parameters, you will get a huge
performance gain by running them in a batch. This is by some databases also known as bulk updates or bulk inserts. Sql2o
supports only homogeneous batches, that is batches with only one query, but with multiple sets of parameters. This is
due to limitations in jdbc.

In the example below, 100 rows are inserted into a table in the database in one batch.

{% highlight java %}

public void insertABunchOfRows(){
    final String sql = "INSERT INTO SomeTable(id, value) VALUES (:id, :value)";

    sql2o.runInTransaction(new StatementRunnable() {
        public void run(Connection connection, Object argument) throws Throwable {
            Query query = connection.createQuery(sql);

            for (int i = 0; i < 100; i++){
                query.addParameter("id", i).addParameter("value", "foo" + i).addToBatch();
            }

            query.executeBatch();
        }
    });
}

{% endhighlight %}

It is almost always a good idea to run batch updates in a transaction, as batch updates are not atomic and therefor
might leave your database in an inconsistent state.