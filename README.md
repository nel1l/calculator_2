# calculator_2
    import pygame
    import math
    
    pygame.init()
    
    WHITE = (179, 175, 204)     # not quite white
    BLACK = (36, 29, 29)        # not quite black
    GRAY = (136, 139, 148)
    RED = (120, 17, 10)         # not quite red
    
    WIDTH, HEIGHT = 600, 500
    MARGIN = 2
    
    def evaluate_expression(expression):
        try:
            result = str(eval(expression, {"__builtins__": None}, {"math": math}))
        except Exception as e:
            result = "Error"
        return result
    
    input_output_height = HEIGHT // 5
    button_area_height = HEIGHT - input_output_height
    
    num_rows = 7
    num_cols = 4
    
    button_area_width = WIDTH
    button_width = (button_area_width - (num_cols + 1) * MARGIN) // num_cols + 1.2
    button_height = (button_area_height - (num_rows + 1) * MARGIN) // num_rows + 1.5
    
    screen = pygame.display.set_mode((WIDTH, HEIGHT))
    pygame.display.set_caption("Command Calculator_endterm")
    
    sound1 = pygame.mixer.Sound("sound1.wav")
    sound2 = pygame.mixer.Sound("sound2.wav")
    
    font = pygame.font.SysFont(None, 30)
    font_res_exp = pygame.font.SysFont(None, 50)
    
    button_labels = {
        "math.sin": "sin", "math.cos": "cos", "math.tan": "tan", "math.log10": "log10",
        "math.exp": "exp", "**": "^", "math.sqrt": "sqrt", "math.log": "log",
        "math.pi": "π", "(": "(", ")": ")", "/": "/",
        "7": "7", "8": "8", "9": "9", "*": "*",
        "4": "4", "5": "5", "6": "6", "-": "-",
        "1": "1", "2": "2", "3": "3", "+": "+",
        "C": "C", ".": ".", "0": "0", "=": "="
    }
    
    button_rects = []
    for row in range(num_rows):
        for col in range(num_cols):
            button_rects.append(pygame.Rect(
                col * (button_width + MARGIN),
                row * (button_height + MARGIN) + input_output_height,
                button_width,
                button_height
            ))
    
    input_expression = ""
    result = ""
    last_operation = ""  # Variable to store the last operation
    
    running = True
    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            elif event.type == pygame.MOUSEBUTTONDOWN:
                if event.button == 1:  # Left mouse button
                    x, y = event.pos
                    for i, rect in enumerate(button_rects):
                        if rect.collidepoint(x, y):
                            button_pressed = list(button_labels.keys())[i]
                            if button_pressed == "=":
                                sound2.play()
                                if last_operation:  # If last_operation is not empty, use it for next calculation
                                    input_expression = result + last_operation
                                result = evaluate_expression(input_expression)
                                last_operation = input_expression[len(result):]  # Store the last operation
                            elif button_pressed == "C":
                                sound1.play()
                                input_expression = ""
                                result = ""
                                last_operation = ""
                            elif button_pressed == "backspace":
                                sound1.play()
                                input_expression = input_expression[:-1]
                            else:
                                sound1.play()
                                input_expression += button_labels[button_pressed]
                            break
            elif event.type == pygame.KEYDOWN:
                if event.unicode.isdigit() or event.unicode == ".":
                    input_expression += event.unicode
                    sound1.play()
                elif event.unicode in "+-*/":
                    input_expression += button_labels[event.unicode]
                    sound1.play()
                elif event.key == pygame.K_RETURN:
                    sound2.play()
                    if last_operation:  # If last_operation is not empty, use it for next calculation
                        input_expression = result + last_operation
                    result = evaluate_expression(input_expression)
                    last_operation = input_expression[len(result):]  # Store the last operation
                elif event.key == pygame.K_BACKSPACE and len(input_expression) > 0:
                    sound1.play()
                    input_expression = input_expression[:-1]
    
        screen.fill(WHITE)
    
        for i, rect in enumerate(button_rects):
            if button_labels[list(button_labels.keys())[i]] == "C":
                button_color = (158, 43, 35)
            else:
                button_color = GRAY
            pygame.draw.rect(screen, button_color, rect)
            button_label = button_labels[list(button_labels.keys())[i]]
            text = font.render(button_label, True, BLACK)
            text_rect = text.get_rect(center=rect.center)
            screen.blit(text, text_rect)
    
        expression_surface = font_res_exp.render(input_expression, True, BLACK)
        expression_rect = expression_surface.get_rect(topright=(WIDTH - MARGIN, MARGIN))
        screen.blit(expression_surface, expression_rect)
    
        result_surface = font_res_exp.render(result, True, RED)
        result_rect = result_surface.get_rect(topright=(WIDTH - MARGIN, MARGIN + 50))
        screen.blit(result_surface, result_rect)
    
        pygame.display.flip()
    
    pygame.quit()
