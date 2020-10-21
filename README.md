Users table:


| user_id    | user_name    | credit      |
|  ------    | ------------  |------------|
| 1          | Moustafa     | 100         |
| 2          | Jonathan     | 200         |
| 3          | Winston      | 10000       |
| 4          | Luis         | 800         |

Transaction table:

| trans_id   | paid_by    | paid_to    | amount   | transacted_on |
|------------|------------|------------|----------|---------------|
| 1          | 1          | 3          | 400      | 2020-08-01    |
| 2          | 3          | 2          | 500      | 2020-08-02    |
| 3          | 2          | 1          | 200      | 2020-08-03    |

Result table:
| user_id    | user_name  | credit     | credit_limit_breached |
|------------|------------|------------|-----------------------|
| 1          | Moustafa   | -100       | Yes                   |
| 2          | Jonathan   | 500        | No                    |
| 3          | Winston    | 9990       | No                    |
| 4          | Luis       | 800        | No                    |


'
select Users.user_id as user_id 
    , Users.user_name as user_name 
    , credit+ifnull(sum(trans),0) as credit
    , case when credit+ifnull(sum(trans),0)>0 then 'No' else 'Yes' end as credit_limit_breached
from(
    select paid_by as user_id, -amount as trans
    from Transaction
    union all
    select paid_to as user_id, amount as trans
    from Transaction
) t right join users on t.user_id=users.user_id
group by user_id
'
