# What is a Project ?
Special projects are about researching projects in Facilities to get equipment and other bonuses.
Special projects are divided in several Specializations. Each Specialization has a tree of researchable projects. 
One entry in this DB is one node in those trees.

# Phases of a Project

Researching a Project is divided in several phases.

* Establishing phase : 
will need to go through that phase when start or re-starting a project
will start from beginning of the phase if cancelled in the middle
* Initial research phase : 
will need to go through that phase only once - if project cancelled after it's done, it won't go through it again
if cancelled in the middle, will remember where it was
* Prototyping phase :
will need to go through that phase in several iterations until project is completed - once completed the Project is fully researched
if cancelled in the middle, will remember where it was
	* Complexity :
	How much progress is gained each prototype iteration. Once reaching 100 the prototype phase is completed.

# Example of project

```python
my_project_token = {
	# 必填项
	# 包含此项目的特殊项目类别
	specialization = my_specialization_token

	# 可选项
	# 项目启动时对所有国家评估的触发条件，用于填充潜在项目池
	# 若条件为真，则为该国创建此项目的实例
	# 作用域：国家。仅允许使用 tag、original_tag 和 has_dlc
	allowed = { ... }

	# 可选项
	# 显示项目时评估的触发条件
	# 若条件为假，项目将被禁用并灰显
	# 作用域：项目。FROM: 国家
	available = { ... }

	# 可选项
	# 显示项目时评估的触发条件
	# 若条件为假，项目将被隐藏
	# 作用域：项目。FROM: 国家
	visible = { ... }
	
	# 可选项
	# 启动项目所需的突破点数成本
	# 可包含来自不同专业化领域的突破点
	# 若省略则默认为项目所属专业化领域的0点
	# 可通过脚本修改值（国家作用域）
	breakthrough_cost = {
		specialization_air = 2
		specialization_nuclear = 1
		specialization_land = {
			base = 1
			modifier = {
				add = -1
				has_tech = improved_medium_tank_chassis
			}
		}
	}

	# 对AI和脚本而言，项目若同时可见且可用则视为"已启用"

	# 可选项
	narrative = {
		# 可选项
		# 项目名称的本地化键
		# 本地化时可用以下对象：
		#    * Country: 拥有项目的国家
		name = name_loc_key
    	# 若提供名称，优先使用 TAG_name_loc_key（TAG为项目所有者标签）
    	# 若存在本地化键 TAG_my_project_token，则使用它
    	# 否则使用 my_project_token

		# 可选项
		# 项目详细描述的本地化键
		# 本地化时可用以下对象：
		#    * SpecialProject: 项目本身
		#    * Country: 拥有项目的国家
		desc = desc_loc_key
    	# 逻辑同名称，但后缀为 _desc
	}
	
	# 可选项
	# 项目的图标GFX键
	icon = GFX_icon
    # 优先级：指定图标 > GFX_TAG_项目令牌 > GFX_项目令牌 > 默认占位符
	
	# 可选项
	# 覆盖专业化层级的蓝图背景图像
	blueprint_image = GFX_generic_tank_blueprint_background
	
	# 必填项（至少1天）
	# 各阶段所需天数（默认0）
	prototype_time = 3

	# 可选项
	# 基础研究和原型阶段的资源消耗
	# 资源不足会降低研究速度
	# 特殊项目成本总是在生产线资源成本之后计算
	# 不允许使用石油
	resource_cost = {
		resources = { steel=5 rubber=12 ... }
	}
	
	# 可选项（默认为定义值，若设为0则报错并使用默认值）
	# 每次原型迭代的进度增加值
	complexity = 10
	
	# 可选项
	# 奖励为空时的权重（默认由NProject::DEFAULT_EMPTY_REWARD_WEIGHT定义）
	# 作用域：ROOT=项目，FROM=国家
	empty_reward_weight = {
		base = 1.0
		modifier = {
			factor = 42.0
			[...条件...]
		}
	}

	# 可选项
	# 项目的AI权重（默认为1）
	ai_will_do = { ... }
	
	# 可选项
	# 项目需所有父节点完成后才可见
	special_project_parent = {
		my_other_project_token
		another_project_token
	}
	
	# 可选项
	# 项目完成时的最终奖励
	project_output = { ... }
	
	# 可选项（数组）
	# 原型阶段每次迭代的独特奖励
	unique_prototype_rewards = {
		dynamic_token1 = { ... }
		{ ... }  # 匿名奖励
	}

	# 可选项（数组）
	# 通用原型奖励（来自奖励数据库）
	generic_prototype_rewards = {
		generic_prototype_reward_token
	}
}
```

# Example of output

This is the output for either : 
* the project output on completion
* or the prototype rewards when iterating

```python
project_output/iteration_output = {
	# 国家效果块（作用域：国家）
	country_effects = {
		add_stability = var:FROM.some_variable_at_projet_level
	}

	# 设施所在州效果块（若无设施则跳过）
	facility_state_effects = {
		add_province_modifier = {
			static_modifiers = { my_modifier }
			province = var:facility_province_id
		}
	}

	# 科学家效果块（若无科学家则跳过）
	scientist_effects = {
		retire = yes
	}

	# 启用装备变体
	enable_equipments = {
		limit = { ... }
		equipment_variant_token
	}

	# 启用装备模块
	enable_equipment_modules = {
		limit = { ... }
		module_token 
	}

	# 启用子单位
	enable_subunits = {
		limit = { ... }
		subunit_token 
	}

	# 子单位属性加成
	sub_unit_bonus = {
		cavalry = { soft_attack = 0.05 }
	}

	# 装备加成
	equipment_bonus = {
		armor = { armor_value = 3 }
		instant = yes  # 是否立即应用于现有变体
	}
}
```
