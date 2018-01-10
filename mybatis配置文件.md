# 基础配置
```
  <resultMap id="BaseResultMap" type="com.alarmserver.model.MyAlarm">
    <id column="id" jdbcType="VARCHAR" property="id" />
    <result column="tenant_id" jdbcType="VARCHAR" property="tenantId" />
    <result column="application_key" jdbcType="VARCHAR" property="applicationKey" />
    <result column="application_name" jdbcType="VARCHAR" property="applicationName" />
    <result column="name" jdbcType="VARCHAR" property="name" />
    <result column="content" jdbcType="VARCHAR" property="content" />
    <result column="level" jdbcType="TINYINT" property="level" />
    <result column="first_time" jdbcType="BIGINT" property="firstTime" />
    <result column="latest_time" jdbcType="BIGINT" property="latestTime" />
    <result column="status" jdbcType="TINYINT" property="status" />
    <result column="confirm_time" jdbcType="BIGINT" property="confirmTime" />
    <result column="confirm_name" jdbcType="VARCHAR" property="confirmName" />
    <result column="close_time" jdbcType="BIGINT" property="closeTime" />
    <result column="close_name" jdbcType="VARCHAR" property="closeName" />
    <result column="close_descr" jdbcType="VARCHAR" property="closeDescr" />
    <result column="assign_name" jdbcType="VARCHAR" property="assignName" />
  </resultMap>
  <sql id="Base_Column_List">
    id, tenant_id, application_key, application_name, `name`, content, `level`, first_time,
    latest_time, `status`, confirm_time, confirm_name, close_time, close_name, close_descr,
    assign_name
  </sql>
```

# 分页查询
```
  <select id="selectByPage" parameterType="com.alarmserver.model.RequestAlarm" resultMap="BaseResultMap">
    SELECT
    <include refid="Base_Column_List" />
    FROM alarm
    WHERE first_time BETWEEN UNIX_TIMESTAMP(#{startTime,jdbcType=VARCHAR}) AND UNIX_TIMESTAMP(#{endTime,jdbcType=VARCHAR})
    AND `status` = #{status,jdbcType=TINYINT}
    AND `level` = #{level,jdbcType=TINYINT}
    <if test="name != null">
      AND `name` LIKE '%${name}%'
    </if>
  </select>
```

# 查询记录数
```
  <select id="selectStatusTotal" parameterType="com.alarmserver.model.RequestAlarm" resultType="java.lang.Long">
    SELECT COUNT(1) FROM alarm
    WHERE first_time BETWEEN UNIX_TIMESTAMP(#{startTime,jdbcType=VARCHAR}) AND UNIX_TIMESTAMP(#{endTime,jdbcType=VARCHAR})
    AND `status` = #{status,jdbcType=TINYINT}
    <if test="name != null">
      AND `name` LIKE '%${name}%'
    </if>
  </select>
```

# 批量添加
不存在就添加，存在就修改，需要设置UNIQUE索引。
```
  <insert id="bulkInsert" parameterType="java.util.List">
    INSERT INTO alarm (id, tenant_id, application_key,
    application_name, `name`, content,
    `level`, first_time, latest_time,
    `status`, confirm_time, confirm_name,
    close_time, close_name, close_descr,
    assign_name)
    VALUES
    <foreach collection="list" item="item" index="index" separator="," >
      (#{item.id,jdbcType=VARCHAR}, #{item.tenantId,jdbcType=VARCHAR}, #{item.applicationKey,jdbcType=VARCHAR},
      #{item.applicationName,jdbcType=VARCHAR}, #{item.name,jdbcType=VARCHAR}, #{item.content,jdbcType=VARCHAR},
      #{item.level,jdbcType=TINYINT}, #{item.firstTime,jdbcType=BIGINT}, #{item.latestTime,jdbcType=BIGINT},
      #{item.status,jdbcType=TINYINT}, #{item.confirmTime,jdbcType=BIGINT}, #{item.confirmName,jdbcType=VARCHAR},
      #{item.closeTime,jdbcType=BIGINT}, #{item.closeName,jdbcType=VARCHAR}, #{item.closeDescr,jdbcType=VARCHAR},
      #{item.assignName,jdbcType=VARCHAR})
    </foreach>
    ON DUPLICATE KEY UPDATE
    `status` = VALUES(`status`),
    latest_time = VALUES(latest_time),
    close_time = VALUES(close_time),
    close_descr = VALUES(close_descr)
  </insert>
```

# 批量修改
```
  <update id="bulkUpdate" parameterType="java.util.List">
    UPDATE alarm
    <trim prefix="SET" suffixOverrides=",">
      <trim prefix="`status`=CASE" suffix="END,">
        <foreach collection="list" item="item" index="index">
          WHEN id = #{item.id,jdbcType=VARCHAR} THEN #{item.status,jdbcType=BIGINT}
        </foreach>
      </trim>
      <trim prefix="confirm_time=CASE" suffix="END,">
        <foreach collection="list" item="item" index="index">
          WHEN id = #{item.id,jdbcType=VARCHAR} THEN #{item.confirmTime,jdbcType=BIGINT}
        </foreach>
      </trim>
      <trim prefix="confirm_name=CASE" suffix="END,">
        <foreach collection="list" item="item" index="index">
          WHEN id = #{item.id,jdbcType=VARCHAR} THEN #{item.confirmName,jdbcType=VARCHAR}
        </foreach>
      </trim>
    </trim>
    WHERE id IN
    <foreach collection="list" index="index" item="item" separator="," open="(" close=")">
      #{item.id,jdbcType=VARCHAR}
    </foreach>
  </update>
```

# 批量删除
```
  <update id="bulkDelete" parameterType="java.util.List">
    DELETE FROM alarm
    WHERE id IN
    <foreach collection="list" index="index" item="item" separator="," open="(" close=")">
      #{item.id,jdbcType=VARCHAR}
    </foreach>
  </update>
```
