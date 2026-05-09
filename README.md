# TORRES 

# Dodge The Creeps - Single File Source Code
# Godot 4
# Attach this script to a Node2D

extends Node2D

# =========================
# SETTINGS
# =========================
var player_speed = 300.0
var creep_speed = 170.0
var spawn_delay = 0.8

var score = 0
var game_over = false

var player
var score_label
var timer

var creeps = []

# =========================
# READY
# =========================
func _ready():
	randomize()
	
	# Background
	var bg = ColorRect.new()
	bg.color = Color(0.18, 0.35, 0.38)
	bg.size = Vector2(360, 640)
	add_child(bg)
	
	# Player
	player = Area2D.new()
	player.position = Vector2(180, 320)
	add_child(player)
	
	var player_sprite = ColorRect.new()
	player_sprite.color = Color.WHITE
	player_sprite.size = Vector2(20, 20)
	player_sprite.position = Vector2(-10, -10)
	player.add_child(player_sprite)
	
	var player_collision = CollisionShape2D.new()
	var player_shape = CircleShape2D.new()
	player_shape.radius = 10
	player_collision.shape = player_shape
	player.add_child(player_collision)
	
	# Score Label
	score_label = Label.new()
	score_label.position = Vector2(150, 20)
	score_label.text = "0"
	score_label.scale = Vector2(2, 2)
	add_child(score_label)
	
	# Title
	var title = Label.new()
	title.text = "Dodge the\nCreeps"
	title.position = Vector2(100, 250)
	title.scale = Vector2(2, 2)
	add_child(title)
	
	# Timer
	timer = Timer.new()
	timer.wait_time = spawn_delay
	timer.timeout.connect(spawn_creep)
	timer.autostart = true
	add_child(timer)

# =========================
# PROCESS
# =========================
func _process(delta):
	if game_over:
		return
	
	move_player(delta)
	move_creeps(delta)
	check_collision()
	update_score(delta)

# =========================
# PLAYER MOVEMENT
# =========================
func move_player(delta):
	var direction = Vector2.ZERO
	
	if Input.is_action_pressed("ui_left"):
		direction.x -= 1
	
	if Input.is_action_pressed("ui_right"):
		direction.x += 1
	
	if Input.is_action_pressed("ui_up"):
		direction.y -= 1
	
	if Input.is_action_pressed("ui_down"):
		direction.y += 1
	
	player.position += direction.normalized() * player_speed * delta
	
	# Screen Bounds
	player.position.x = clamp(player.position.x, 15, 345)
	player.position.y = clamp(player.position.y, 15, 625)

# =========================
# SPAWN ENEMY
# =========================
func spawn_creep():
	if game_over:
		return
	
	var creep = Area2D.new()
	add_child(creep)
	
	var sprite = ColorRect.new()
	sprite.color = Color.RED
	sprite.size = Vector2(18, 18)
	sprite.position = Vector2(-9, -9)
	creep.add_child(sprite)
	
	var collision = CollisionShape2D.new()
	var shape = CircleShape2D.new()
	shape.radius = 9
	collision.shape = shape
	creep.add_child(collision)
	
	# Random Spawn Side
	var side = randi() % 4
	
	match side:
		0:
			creep.position = Vector2(randf_range(0, 360), -20)
		1:
			creep.position = Vector2(randf_range(0, 360), 660)
		2:
			creep.position = Vector2(-20, randf_range(0, 640))
		3:
			creep.position = Vector2(380, randf_range(0, 640))
	
	creeps.append(creep)

# =========================
# MOVE ENEMIES
# =========================
func move_creeps(delta):
	for creep in creeps:
		if creep == null:
			continue
		
		var direction = (player.position - creep.position).normalized()
		creep.position += direction * creep_speed * delta

# =========================
# COLLISION
# =========================
func check_collision():
	for creep in creeps:
		if creep == null:
			continue
		
		if player.position.distance_to(creep.position) < 18:
			end_game()

# =========================
# SCORE
# =========================
func update_score(delta):
	score += delta * 10
	score_label.text = str(int(score))

# =========================
# GAME OVER
# =========================
func end_game():
	game_over = true
	timer.stop()
	score_label.text = "GAME OVER\nScore: " + str(int(score))

Project → Project Settings → Input Map

ui_up
ui_down
ui_left
ui_right

Display → Window
Width = 360
Height = 640





