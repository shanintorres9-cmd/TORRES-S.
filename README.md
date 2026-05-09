# TORRES-S.

# Main.gd
# Attach this script to a Node2D in Godot 4

extends Node2D

@export var creep_scene: PackedScene
@export var player_speed := 300.0
@export var creep_speed := 180.0
@export var spawn_time := 1.0

var score := 0
var game_over := false

@onready var player = $Player
@onready var score_label = $CanvasLayer/ScoreLabel
@onready var spawn_timer = $SpawnTimer

func _ready():
	randomize()
	spawn_timer.wait_time = spawn_time
	spawn_timer.start()
	update_score()

func _process(delta):
	if game_over:
		return
	
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
	
	# Keep player inside screen
	player.position.x = clamp(player.position.x, 20, 340)
	player.position.y = clamp(player.position.y, 20, 620)
	
	score += delta * 10
	update_score()

func update_score():
	score_label.text = str(int(score))

func _on_spawn_timer_timeout():
	if game_over:
		return
	
	var creep = creep_scene.instantiate()
	add_child(creep)
	
	var side = randi() % 4
	var viewport_size = get_viewport_rect().size
	
	match side:
		0:
			creep.position = Vector2(randf_range(0, viewport_size.x), -20)
		1:
			creep.position = Vector2(randf_range(0, viewport_size.x), viewport_size.y + 20)
		2:
			creep.position = Vector2(-20, randf_range(0, viewport_size.y))
		3:
			creep.position = Vector2(viewport_size.x + 20, randf_range(0, viewport_size.y))
	
	creep.target = player
	creep.speed = creep_speed

func game_end():
	game_over = true
	spawn_timer.stop()
	score_label.text = "Game Over\nScore: " + str(int(score))
