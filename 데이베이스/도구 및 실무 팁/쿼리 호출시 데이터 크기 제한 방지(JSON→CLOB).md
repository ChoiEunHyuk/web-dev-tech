```xml
<select id="actionLoginWithRoleInfo" parameterType="egovframework.com.cmm.LoginVO" resultType="egovframework.com.cmm.LoginVO">
	 WITH DISTINCT_ROLES AS (
	    SELECT DISTINCT 
	        e.emplyr_id AS id,
	        a.role_code
	    FROM COMTNEMPLYRINFO e
	    JOIN COMTNGRSCRTYESTBS r ON e.group_id = r.group_id
	    JOIN COMTNAUTHORROLERELATE a ON r.author_code = a.author_code
	    WHERE e.emplyr_id = #{id}
	      AND emplyr_sttus_code = 'P'
	),
	DISTINCT_ROLE_INFO AS (
	    SELECT DISTINCT 
	        e.emplyr_id AS id,
	        f.CLASS_NM AS class_nm, 
	        f.ROLE_URL AS role_url, 
	        f.SYS_CODE AS sys_code, 
	        C.CODE_NM AS code_nm
	    FROM COMTNEMPLYRINFO e
	    JOIN COMTNGRSCRTYESTBS r ON e.group_id = r.group_id
	    JOIN COMTNAUTHORROLERELATE a ON r.author_code = a.author_code
	    JOIN COMTNROLEINFO f ON a.ROLE_CODE = f.ROLE_CODE 
	    LEFT JOIN COMTCCMMNDETAILCODE c ON c.CODE = f.SYS_CODE AND c.CODE_ID = 'SYSADM' AND c.USE_AT = 'Y'
	    WHERE e.emplyr_id = #{id}
	      AND emplyr_sttus_code = 'P'
	)
	 SELECT 
		e.emplyr_id AS id,
		e.user_nm AS name,
		e."password" AS password,
		e.ihidnum AS ihidNum,
		e.email_adres AS email,
		'USR' AS userSe,
		e.orgnzt_id AS orgnztId,
		e.esntl_id AS uniqId,
		e.group_id AS groupId,
		(SELECT JSON_ARRAYAGG(role_code RETURNING CLOB) FROM DISTINCT_ROLES WHERE id = e.emplyr_id) AS role_code_json,
	    (SELECT JSON_ARRAYAGG(
		    JSON_OBJECT(
		        'CLASS_NM' VALUE class_nm, 
		        'ROLE_URL' VALUE role_url, 
		        'SYS_CODE' VALUE sys_code, 
		        'CODE_NM' VALUE code_nm
		    )
		    ORDER BY
		        CASE 
		            WHEN sys_code = 'DTF' THEN 1
		            WHEN sys_code = 'FLD' THEN 2
		            WHEN sys_code = 'WTUT' THEN 3
		            WHEN sys_code = 'LGW' THEN 4
		            WHEN sys_code = 'RDD' THEN 5
		            WHEN sys_code = 'NT' THEN 6
		            ELSE 7
		        END
		    RETURNING CLOB
		) FROM DISTINCT_ROLE_INFO WHERE id = e.emplyr_id) AS role_info_json,
		(SELECT JSON_ARRAYAGG(
            j.code RETURNING CLOB
        ) AS syscode_json  
		FROM COMTNJOINCCMMNDETAILCODE j 
		WHERE j.emplyr_id = #{id}) AS user_syscode_json,
		ROUND(NVL(SYSDATE - CAST(e.CHG_PWD_LAST_PNTTM AS DATE), 0), 4) AS passedDayChgPw
		FROM COMTNEMPLYRINFO e
		JOIN COMTNGRSCRTYESTBS r ON e.group_id = r.group_id
		JOIN COMTNAUTHORROLERELATE a ON r.author_code = a.author_code
		JOIN COMTNROLEINFO F ON a.ROLE_CODE = f.ROLE_CODE 
		LEFT JOIN COMTCCMMNDETAILCODE C ON C.CODE = f.SYS_CODE AND C.CODE_ID = 'SYSADM' AND C.USE_AT = 'Y'
		WHERE e.emplyr_id = #{id}
		  AND e."password" = #{password}
		  AND emplyr_sttus_code = 'P'
		 GROUP BY  e.emplyr_id, e.user_nm, e."password", e.ihidnum, e.email_adres ,e.orgnzt_id, e.esntl_id ,e.group_id, e.chg_pwd_last_pnttm 
	</select>
```

위의 MyBatis 코드에서 로그인 시 불러오는 사용자 정보의 크기 제한을 방지하기 위해 **CLOB (Character Large Object)** 타입을 사용하여 JSON 데이터를 처리하고 있습니다.

### **크기 제한을 방지하기 위한 코드 요소**

1. **JSON 데이터를 CLOB으로 변환하여 반환**
    - `JSON_ARRAYAGG(... RETURNING CLOB)`
    - `JSON_OBJECT(... RETURNING CLOB)`

```sql
(SELECT JSON_ARRAYAGG(role_code RETURNING CLOB) FROM DISTINCT_ROLES WHERE id = e.emplyr_id) AS role_code_json,
```

- 역할 코드(role_code)를 JSON 배열로 변환하여 CLOB 타입으로 반환 → 데이터 크기 제한 없음.

```sql
(SELECT JSON_ARRAYAGG(
    JSON_OBJECT(
        'CLASS_NM' VALUE class_nm,
        'ROLE_URL' VALUE role_url,
        'SYS_CODE' VALUE sys_code,
        'CODE_NM' VALUE code_nm
    )
    ORDER BY
        CASE
            WHEN sys_code = 'DTF' THEN 1
            WHEN sys_code = 'FLD' THEN 2
            WHEN sys_code = 'WTUT' THEN 3
            WHEN sys_code = 'LGW' THEN 4
            WHEN sys_code = 'RDD' THEN 5
            WHEN sys_code = 'NT' THEN 6
            ELSE 7
        END
    RETURNING CLOB
) FROM DISTINCT_ROLE_INFO WHERE id = e.emplyr_id) AS role_info_json,
```

- 역할 정보(role_info)를 JSON 객체 배열로 변환하고, 이를 CLOB 타입으로 변환하여 반환 → 데이터 크기 제한 없음.

```sql
(SELECT JSON_ARRAYAGG(
    j.code RETURNING CLOB
) AS syscode_json
FROM COMTNJOINCCMMNDETAILCODE j
WHERE j.emplyr_id = #{id}) AS user_syscode_json,
```

- 사용자 시스템 코드(user_syscode)를 JSON 배열로 변환하여 CLOB으로 반환 → 데이터 크기 제한 없음.

### **CLOB을 사용하는 이유**

1. **일반적인 VARCHAR2는 최대 4000바이트 제한이 있음.**
2. **CLOB은 매우 큰 데이터를 저장할 수 있으며, JSON과 같은 대량의 문자열 데이터 저장에 적합함.**
3. **JSON_ARRAYAGG 및 JSON_OBJECT를 사용할 때 RETURNING CLOB을 지정하면 데이터 크기 제한을 방지할 수 있음.**
