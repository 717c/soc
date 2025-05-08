 def check_track_collision(self, track_surface):
        """检查赛车是否在赛道上（简化版）"""
        # 这里我们简化处理，实际应该检测赛车与赛道边缘的碰撞
        # 这里我们只是检查赛车中心是否在赛道上
        x, y = self.rect.center
        # 假设赛道是屏幕中央的矩形区域
        track_rect = pygame.Rect(WIDTH//4, HEIGHT//4, WIDTH//2, HEIGHT//2)
        return track_rect.collidepoint(x, y)
    
    def draw(self, surface):
        """绘制赛车"""
        # 创建旋转后的表面
        rotated_image = pygame.transform.rotate(self.image, self.angle)
        rotated_rect = rotated_image.get_rect(center=self.rect.center)
        surface.blit(rotated_image, rotated_rect.topleft)
        
        # 绘制碰撞检测点（调试用）
        for point in self.collision_points:
            pygame.draw.rect(surface, RED, point, 1)
 
# 障碍物类
class Obstacle:
    def __init__(self, x, y, width, height):
        self.rect = pygame.Rect(x, y, width, height)
        self.color = random.choice([GREEN, BLUE, YELLOW])
    
    def draw(self, surface):
        pygame.draw.rect(surface, self.color, self.rect)
 
# 赛道类
class Track:
    def __init__(self):
        self.surface = pygame.Surface((WIDTH, HEIGHT))
        self.surface.fill(GRAY)
        
        # 绘制赛道边界（简化版）
        pygame.draw.rect(self.surface, WHITE, (WIDTH//4, HEIGHT//4, WIDTH//2, HEIGHT//2), 5)
        
        # 添加一些障碍物
        self.obstacles = []
        for _ in range(5):
            x = random.randint(WIDTH//4 + 20, WIDTH*3//4 - 20)
            y = random.randint(HEIGHT//4 + 20, HEIGHT*3//4 - 20)
            width = random.randint(30, 60)
            height = random.randint(30, 60)
            self.obstacles.append(Obstacle(x, y, width, height))
    
    def draw(self, surface):
        surface.blit(self.surface, (0, 0))
        for obstacle in self.obstacles:
            obstacle.draw(surface)
    
    def check_collisions(self, car):
        """检查赛车与障碍物的碰撞"""
        # 检查与赛道边界的碰撞（简化版）
        if not car.check_track_collision(self.surface):
            return True
        
        # 检查与障碍物的碰撞
        for obstacle in self.obstacles:
            if car.rect.colliderect(obstacle.rect):
                return True
        return False
 
# 游戏主类
class RacingGame:
    def __init__(self):
        self.clock = pygame.time.Clock()
        self.font = pygame.font.SysFont(None, 36)
        
        # 创建赛车和赛道
        self.car = Car(WIDTH//2, HEIGHT//2, "car.png")  # 假设有car.png图片
        self.track = Track()
        
        # 游戏状态
        self.running = True
        self.game_over = False
        self.score = 0
        self.lap_time = 0
        self.start_time = pygame.time.get_ticks()
    
    def run(self):
        """游戏主循环"""
        while self.running:
            self.handle_events()
            if not self.game_over:
                self.update()
            self.render()
            self.clock.tick(60)  # 60 FPS
