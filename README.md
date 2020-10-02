# Welcome to Learning with Astra #

If you have attended recently our workshops here, we ask you to create your own Github repo showing off your learning journey with Astra. You could use this repo here as a start: fork it and update it with your own examples.

## Explain your use case ##

An application which tracks the user's mood in time. E.g. every day the application asks the user how they day was, and they answer in a range of one to five. Later on, we might want to see what the user's average is, which days were the best, and so on.

## Create your own tables on Astra ##

My table:

```
create table moods_by_user ( 
  name text, 
  time timeuuid, 
  mood int, 
  primary key ((name), time)) 
  with clustering order by (time desc);
```

## Insert some data ##

My inserts:

```
insert into moods_by_user (name, time, mood) values ('Sue', now(), 5);
insert into moods_by_user (name, time, mood) values ('Sue', now(), 3);
insert into moods_by_user (name, time, mood) values ('Megan', now(), 3);
insert into moods_by_user (name, time, mood) values ('Sue', now(), 1);
insert into moods_by_user (name, time, mood) values ('Megan', now(), 5);
```

The output:

```
select * from moods_by_user;
...
 name  | time                                 | mood
-------+--------------------------------------+------
   Sue | 21a99de0-04e8-11eb-ab1d-91ce55467f2a |    1
   Sue | 1b63c820-04e8-11eb-ab1d-91ce55467f2a |    3
   Sue | 18be71b0-04e8-11eb-ab1d-91ce55467f2a |    5
 Megan | 23ec61f0-04e8-11eb-ab1d-91ce55467f2a |    5
 Megan | 1fc24040-04e8-11eb-ab1d-91ce55467f2a |    3
...
...
```

## Experiment with CRUD and show the outputs: ##

Update (say, a user changed their mind):

```
update moods_by_user set mood = 5 where name = 'Sue' and time = 21a99de0-04e8-11eb-ab1d-91ce55467f2a;

SELECT * FROM moods_by_user;

 name  | time                                 | mood
-------+--------------------------------------+------
   Sue | 21a99de0-04e8-11eb-ab1d-91ce55467f2a |    5
   Sue | 1b63c820-04e8-11eb-ab1d-91ce55467f2a |    3
   Sue | 18be71b0-04e8-11eb-ab1d-91ce55467f2a |    5
 Megan | 23ec61f0-04e8-11eb-ab1d-91ce55467f2a |    5
 Megan | 1fc24040-04e8-11eb-ab1d-91ce55467f2a |    3
```

Delete (the user decided they don't want to share their mood that day):
```
delete from moods_by_user where name = 'Sue' and time = 1b63c820-04e8-11eb-ab1d-91ce55467f2a;

SELECT * FROM moods_by_user;

 name  | time                                 | mood
-------+--------------------------------------+------
   Sue | 21a99de0-04e8-11eb-ab1d-91ce55467f2a |    5
   Sue | 18be71b0-04e8-11eb-ab1d-91ce55467f2a |    5
 Megan | 23ec61f0-04e8-11eb-ab1d-91ce55467f2a |    5
 Megan | 1fc24040-04e8-11eb-ab1d-91ce55467f2a |    3
```

Other commands:

```
// seeing how the mood changes in time
select * from moods_by_user where name = 'Megan' order by time;

 name  | time                                 | mood
-------+--------------------------------------+------
 Megan | 1fc24040-04e8-11eb-ab1d-91ce55467f2a |    3
 Megan | 23ec61f0-04e8-11eb-ab1d-91ce55467f2a |    5
 Megan | 13b1c2c0-04e9-11eb-ab1d-91ce55467f2a |    4
 
 
 //displaying the timeuuid in a user-readable format
 select name, toDate(time), mood from moods_by_user;

 name  | system.todate(time) | mood
-------+---------------------+------
   Sue |          2020-10-02 |    5
   Sue |          2020-10-02 |    5
  John |          2020-10-02 |    4
 Alice |          2020-10-02 |    3
 Megan |          2020-10-02 |    4
 Megan |          2020-10-02 |    5
 Megan |          2020-10-02 |    3
 
 //getting average mood for user
 select name, avg(mood) from moods_by_user where name = 'Megan';

 name  | system.avg(mood)
-------+------------------
 Megan |                4

//it turns out avg(int) = int, so I need to cast to double to make it more meaningful
KVUser@cqlsh:killrvideo> select name, avg(cast(mood as double)) from moods_by_user where name = 'Sue';

 name | system.avg(cast(mood as double))
------+----------------------------------
  Sue |                          4.33333
  
```

Thanks DataStax Academy team for the workshop, and for the challenge to make the queries my own during this exercise :)


