#mysql调优实战
- 通过慢查询日志发现这条语句查询时间过长，于是对他优化
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-08/89611065543800.png?Expires=4750483768&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=iWcFg2RO75iQiyh1LyQLZ7wV6e8%3D)
```sql
SELECT hc.id AS 'id', cname, user_name AS 'clientM', telephone, address
	, hc.remark AS remark, join_net_price AS 'joinNetPrice', equipment_installation_price AS 'equipmentInstallationPrice', total, valid
	, newjoin, arrear, scrap, efficient
FROM hl_customers hc
	LEFT JOIN (
		SELECT cid, total, valid, newJoin, arrear
			, scrap
			, CONCAT(LEFT((valid + newjoin) / (total - scrap) * 100, 5), '%') AS 'efficient'
		FROM (
			SELECT c.id AS 'cid', COUNT(0) AS 'total'
				, SUM(IF(a.join_internet_end_time > NOW()
					AND a.is_scrap IS NULL, 1, 0)) AS 'valid'
				, SUM(IF(a.join_internet_end_time IS NULL
				AND DATE_ADD(a.join_time, INTERVAL 1 YEAR) > NOW()
				AND a.is_scrap IS NULL, 1, 0)) AS 'newJoin'
				, SUM(IF((a.join_internet_end_time < NOW()
						AND a.is_scrap IS NULL)
					OR DATE_ADD(a.join_time, INTERVAL 1 YEAR) < NOW()
					AND a.join_internet_end_time IS NULL
					AND a.is_scrap IS NULL, 1, 0)) AS 'arrear'
				, SUM(IF(a.is_scrap IS NOT NULL, 1, 0)) AS 'scrap'
			FROM hl_vehicle a
				INNER JOIN hl_y_vehicle b ON a.y_vehicle_id = b.id
				INNER JOIN hl_customers c ON a.customer_id = c.id
				INNER JOIN hl_sys_user d ON c.cm_user_id = d.id
			GROUP BY c.cname
		) e
	) vc
	ON hc.id = vc.cid
	LEFT JOIN hl_sys_user hs ON hc.cm_user_id = hs.id
LIMIT 0, 10;
```
####这里记录两个命令 查询是否开启mysql缓存和修改mysql缓存
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-08/89875364616900.png?Expires=4750484032&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=WK6cDlrsFNeG558sxrRg1oqolis%3D)
- 执行查询语句可以看到执行时间和获取时间，可以看到总共是用了300毫秒
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-08/90113121043100.png?Expires=4750484270&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=V3yfjUsiCRj4Z0tVElbU8y81bz4%3D)
- 用explaion命令查看查询详情
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-08/90386451143700.png?Expires=4750484543&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=%2FSsgKj8VdCWK7%2FZ6%2FbE1d9vssGM%3D)
一般group by 和order by的字段如果没有索引的话 会造成文件排序
分析sql发现最里面是一个子查询，所以先优化里面的内容，explain查看
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-08/90602634092300.png?Expires=4750484759&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=GPSIhNByEuVkC0nXar8Wfyy%2BOdo%3D)
发现cname字段用到了group by（会产生临时表和文件排序） ，所以给cname字段创建索引`create index cname_index on hl_customers(cname)`
创建索引之后，分组直接基于索引完成（这样的查询称为覆盖索引查询），不会再产生临时表和文件排序
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-08/90859090019900.png?Expires=4750485016&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=GUXEG922Zcv%2BIpEsWwiAMF%2F%2Fuyc%3D)


