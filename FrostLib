local Library = {}
Library.Windows = {}

local function New(ClassName, Properties)
	local Object = Instance.new(ClassName)
	if Properties then 
		for Property, Value in next, Properties do
			Object[Property] = Value
		end 
	end
	return Object
end

local TweenService = game:GetService("TweenService")

function Library:CreateWindow(title, subtitle)
	local Window = {}
	local windowIndex = #Library.Windows + 1

	local SeizureUI = New("ScreenGui", {
		Name = "SeizureUI_" .. windowIndex,
		Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui"),
		ZIndexBehavior = Enum.ZIndexBehavior.Sibling,
		ResetOnSpawn = false
	})

	local Background = New("Frame", {
		Name = "Background",
		Parent = SeizureUI,
		AnchorPoint = Vector2.new(0.5, 0.5),
		BackgroundColor3 = Color3.fromRGB(7, 7, 7),
		BorderSizePixel = 0,
		ClipsDescendants = true,
		Position = UDim2.new(0.540589452 + (windowIndex - 1) * 0.05, 0, 0.453049481, 0),
		Size = UDim2.new(0, 0, 0, 0),
		BackgroundTransparency = 1
	})

	New("UICorner", {
		CornerRadius = UDim.new(0.0199999996, 0),
		Parent = Background
	})

	local BackgroundStroke = New("UIStroke", {
		Color = Color3.fromRGB(25, 25, 25),
		Thickness = 1,
		Transparency = 1,
		Parent = Background
	})

	TweenService:Create(Background, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
		Size = UDim2.new(0, 652, 0, 400),
		BackgroundTransparency = 0
	}):Play()

	task.wait(0.3)
	TweenService:Create(BackgroundStroke, TweenInfo.new(0.3), {
		Transparency = 0
	}):Play()

	New("TextLabel", {
		Name = "title",
		Parent = Background,
		BackgroundTransparency = 1,
		Position = UDim2.new(0.0295828599, 0, 0.0304999929, 0),
		Size = UDim2.new(0, 548, 0, 21),
		Font = Enum.Font.GothamMedium,
		Text = title or "RCash Hub",
		TextColor3 = Color3.fromRGB(255, 255, 255),
		TextSize = 14,
		TextXAlignment = Enum.TextXAlignment.Left
	})

	New("TextLabel", {
		Name = "subtitle",
		Parent = Background,
		BackgroundTransparency = 1,
		Position = UDim2.new(0.0295828599, 0, 0.0639999956, 0),
		Size = UDim2.new(0, 548, 0, 21),
		Font = Enum.Font.Gotham,
		Text = subtitle or "@RdotA",
		TextColor3 = Color3.fromRGB(150, 150, 150),
		TextSize = 12,
		TextXAlignment = Enum.TextXAlignment.Left
	})

	local ContentBackground = New("Frame", {
		Parent = Background,
		BackgroundColor3 = Color3.fromRGB(15, 15, 15),
		BackgroundTransparency = 0.3,
		BorderSizePixel = 0,
		Position = UDim2.new(0.237729788, 0, 0.115000002, 0),
		Size = UDim2.new(0, 497, 0, 354)
	})

	New("UICorner", {
		CornerRadius = UDim.new(0.00999999978, 0),
		Parent = ContentBackground
	})

	New("UIStroke", {
		Color = Color3.fromRGB(25, 25, 25),
		Thickness = 1,
		Parent = ContentBackground
	})

	local TabContainer = New("Frame", {
		Name = "TabContainer",
		Parent = Background,
		BackgroundTransparency = 1,
		Position = UDim2.new(0.019, 0, 0.141499937, 0),
		Size = UDim2.new(0, 135, 0, 324)
	})

	New("UIListLayout", {
		Parent = TabContainer,
		SortOrder = Enum.SortOrder.LayoutOrder,
		Padding = UDim.new(0, 8)
	})

	local ContentContainer = New("ScrollingFrame", {
		Name = "ContentContainer",
		Parent = Background,
		BackgroundTransparency = 1,
		BorderSizePixel = 0,
		Position = UDim2.new(0.236791313, 0, 0.116499938, 0),
		Size = UDim2.new(0.763208568, 0, 0.88350004, 0),
		CanvasSize = UDim2.new(0, 0, 0, 0),
		ScrollBarThickness = 4
	})

	New("UIPadding", {
		Parent = ContentContainer,
		PaddingLeft = UDim.new(0, 10),
		PaddingRight = UDim.new(0, 10),
		PaddingTop = UDim.new(0, 10)
	})

	local ContentLayout = New("UIListLayout", {
		Parent = ContentContainer,
		SortOrder = Enum.SortOrder.LayoutOrder,
		Padding = UDim.new(0, 8)
	})

	ContentLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
		ContentContainer.CanvasSize = UDim2.new(0, 0, 0, ContentLayout.AbsoluteContentSize.Y + 20)
	end)

	Window.Tabs = {}
	Window.CurrentTab = nil

	local dragging, dragInput, dragStart, startPos, dragTween

	Background.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			dragStart = input.Position
			startPos = Background.Position

			if dragTween then
				dragTween:Cancel()
			end

			input.Changed:Connect(function()
				if input.UserInputState == Enum.UserInputState.End then
					dragging = false
				end
			end)
		end
	end)

	Background.InputChanged:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseMovement then
			dragInput = input
		end
	end)

	game:GetService("UserInputService").InputChanged:Connect(function(input)
		if input == dragInput and dragging then
			local delta = input.Position - dragStart
			local newPosition = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)

			if dragTween then
				dragTween:Cancel()
			end

			dragTween = TweenService:Create(Background, TweenInfo.new(0.1, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
				Position = newPosition
			})
			dragTween:Play()
		end
	end)

	function Window:CreateTab(name)
		local Tab = {}
		local isFirstTab = #Window.Tabs == 0

		local TabButton = New("TextButton", {
			Name = "Tab_" .. name,
			Parent = TabContainer,
			BackgroundColor3 = Color3.fromRGB(15, 15, 15),
			BackgroundTransparency = isFirstTab and 0 or 1,
			BorderSizePixel = 0,
			Size = UDim2.new(0.982876241, 0, 0, 35),
			Font = Enum.Font.Gotham,
			Text = name,
			TextColor3 = isFirstTab and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(150, 150, 150),
			TextSize = 14,
			TextXAlignment = Enum.TextXAlignment.Left,
			AutoButtonColor = false
		})

		New("UIPadding", {
			Parent = TabButton,
			PaddingLeft = UDim.new(0, 5)
		})

		New("UICorner", {
			CornerRadius = UDim.new(0.1, 0),
			Parent = TabButton
		})

		local TabStroke = New("UIStroke", {
			Color = Color3.fromRGB(116, 116, 116),
			Thickness = 1,
			Transparency = isFirstTab and 0.80 or 1,
			ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
			Parent = TabButton
		})

		TabButton.MouseButton1Click:Connect(function()
			if Window.CurrentTab == Tab then return end

			for _, tab in pairs(Window.Tabs) do
				TweenService:Create(tab.Button, TweenInfo.new(0.2), {
					BackgroundTransparency = 1,
					TextColor3 = Color3.fromRGB(150, 150, 150)
				}):Play()

				local stroke = tab.Button:FindFirstChildOfClass("UIStroke")
				if stroke then
					TweenService:Create(stroke, TweenInfo.new(0.2), {
						Transparency = 1
					}):Play()
				end
			end

			TweenService:Create(TabButton, TweenInfo.new(0.2), {
				BackgroundTransparency = 0,
				TextColor3 = Color3.fromRGB(255, 255, 255)
			}):Play()

			TweenService:Create(TabStroke, TweenInfo.new(0.2), {
				Transparency = 0.80
			}):Play()

			Window.CurrentTab = Tab
			Window:RefreshContent()
		end)

		Tab.Button = TabButton
		Tab.Elements = {}

		table.insert(Window.Tabs, Tab)

		if isFirstTab then
			Window.CurrentTab = Tab
		end

		function Tab:AddParagraph(titleText, contentText)
			local Paragraph = {
				Type = "Paragraph",
				Title = titleText,
				Content = contentText,
				Visible = true
			}

			table.insert(Tab.Elements, Paragraph)
			Window:RefreshContent()

			return {
				Hide = function()
					Paragraph.Visible = false
					Window:RefreshContent()
				end,
				Show = function()
					Paragraph.Visible = true
					Window:RefreshContent()
				end
			}
		end

		function Tab:AddButton(text, callback)
			local Button = {
				Type = "Button",
				Text = text,
				Callback = callback,
				Visible = true
			}

			table.insert(Tab.Elements, Button)
			Window:RefreshContent()

			return {
				Hide = function()
					Button.Visible = false
					Window:RefreshContent()
				end,
				Show = function()
					Button.Visible = true
					Window:RefreshContent()
				end
			}
		end

		function Tab:AddToggle(text, default, callback)
			local Toggle = {
				Type = "Toggle",
				Text = text,
				Value = default or false,
				Callback = callback,
				Visible = true
			}

			table.insert(Tab.Elements, Toggle)
			Window:RefreshContent()

			return {
				Set = function(self, value)
					Toggle.Value = value
					Window:RefreshContent()
				end,
				Hide = function()
					Toggle.Visible = false
					Window:RefreshContent()
				end,
				Show = function()
					Toggle.Visible = true
					Window:RefreshContent()
				end
			}
		end

		return Tab
	end

	function Window:RefreshContent()
		for _, child in pairs(ContentContainer:GetChildren()) do
			if child:IsA("Frame") then
				child:Destroy()
			end
		end

		if not Window.CurrentTab then return end

		for _, element in pairs(Window.CurrentTab.Elements) do
			if not element.Visible then continue end

			if element.Type == "Paragraph" then
				local Paragraph = New("Frame", {
					Name = "Paragraph",
					Parent = ContentContainer,
					BackgroundColor3 = Color3.fromRGB(20, 20, 20),
					BorderSizePixel = 0,
					Size = UDim2.new(1, 0, 0, 100)
				})

				New("UICorner", {
					CornerRadius = UDim.new(0.05, 0),
					Parent = Paragraph
				})

				New("UIStroke", {
					Color = Color3.fromRGB(25, 25, 25),
					Thickness = 1,
					Parent = Paragraph
				})

				New("UIPadding", {
					Parent = Paragraph,
					PaddingBottom = UDim.new(0, 12),
					PaddingLeft = UDim.new(0, 12),
					PaddingRight = UDim.new(0, 12),
					PaddingTop = UDim.new(0, 12)
				})

				local TitleLabel = New("TextLabel", {
					Name = "Title",
					Parent = Paragraph,
					BackgroundTransparency = 1,
					Size = UDim2.new(1, 0, 0, 20),
					Font = Enum.Font.GothamMedium,
					Text = element.Title,
					TextColor3 = Color3.fromRGB(255, 255, 255),
					TextSize = 14,
					TextWrapped = true,
					TextXAlignment = Enum.TextXAlignment.Left,
					TextYAlignment = Enum.TextYAlignment.Top
				})

				local ContentLabel = New("TextLabel", {
					Name = "Content",
					Parent = Paragraph,
					BackgroundTransparency = 1,
					Position = UDim2.new(0, 0, 0, 22),
					Size = UDim2.new(1, 0, 1, -22),
					Font = Enum.Font.Gotham,
					Text = element.Content,
					TextColor3 = Color3.fromRGB(150, 150, 150),
					TextSize = 13,
					TextWrapped = true,
					TextXAlignment = Enum.TextXAlignment.Left,
					TextYAlignment = Enum.TextYAlignment.Top
				})

				task.spawn(function()
					task.wait()
					local textService = game:GetService("TextService")
					local titleSize = textService:GetTextSize(element.Title, 14, Enum.Font.GothamMedium, Vector2.new(Paragraph.AbsoluteSize.X - 24, math.huge))
					local contentSize = textService:GetTextSize(element.Content, 13, Enum.Font.Gotham, Vector2.new(Paragraph.AbsoluteSize.X - 24, math.huge))
					Paragraph.Size = UDim2.new(1, 0, 0, titleSize.Y + contentSize.Y + 32)
				end)

			elseif element.Type == "Button" then
				local ButtonFrame = New("Frame", {
					Name = "Button",
					Parent = ContentContainer,
					BackgroundColor3 = Color3.fromRGB(20, 20, 20),
					BorderSizePixel = 0,
					Size = UDim2.new(1, 0, 0, 42)
				})

				New("UICorner", {
					CornerRadius = UDim.new(0.1, 0),
					Parent = ButtonFrame
				})

				New("UIStroke", {
					Color = Color3.fromRGB(25, 25, 25),
					Thickness = 1,
					Parent = ButtonFrame
				})

				New("TextLabel", {
					Parent = ButtonFrame,
					BackgroundTransparency = 1,
					Position = UDim2.new(0.026, 0, 0, 0),
					Size = UDim2.new(0.95, 0, 1, 0),
					Font = Enum.Font.GothamMedium,
					Text = element.Text,
					TextColor3 = Color3.fromRGB(255, 255, 255),
					TextSize = 14,
					TextXAlignment = Enum.TextXAlignment.Left
				})

				local ClickButton = New("TextButton", {
					Parent = ButtonFrame,
					BackgroundTransparency = 1,
					Size = UDim2.new(1, 0, 1, 0),
					Text = "",
					AutoButtonColor = false
				})

				ClickButton.MouseButton1Click:Connect(function()
					TweenService:Create(ButtonFrame, TweenInfo.new(0.1), {BackgroundColor3 = Color3.fromRGB(30, 30, 30)}):Play()
					task.wait(0.1)
					TweenService:Create(ButtonFrame, TweenInfo.new(0.1), {BackgroundColor3 = Color3.fromRGB(20, 20, 20)}):Play()

					if element.Callback then
						element.Callback()
					end
				end)

			elseif element.Type == "Toggle" then
				local ToggleFrame = New("Frame", {
					Name = "Toggle",
					Parent = ContentContainer,
					BackgroundColor3 = Color3.fromRGB(20, 20, 20),
					BorderSizePixel = 0,
					Size = UDim2.new(1, 0, 0, 42)
				})

				New("UICorner", {
					CornerRadius = UDim.new(0.1, 0),
					Parent = ToggleFrame
				})

				New("UIStroke", {
					Color = Color3.fromRGB(25, 25, 25),
					Thickness = 1,
					Parent = ToggleFrame
				})

				New("TextLabel", {
					Parent = ToggleFrame,
					BackgroundTransparency = 1,
					Position = UDim2.new(0.026, 0, 0, 0),
					Size = UDim2.new(0.85, 0, 1, 0),
					Font = Enum.Font.GothamMedium,
					Text = element.Text,
					TextColor3 = Color3.fromRGB(255, 255, 255),
					TextSize = 14,
					TextXAlignment = Enum.TextXAlignment.Left
				})

				local Indicator = New("Frame", {
					Parent = ToggleFrame,
					AnchorPoint = Vector2.new(1, 0.5),
					BackgroundColor3 = element.Value and Color3.fromRGB(230, 230, 230) or Color3.fromRGB(30, 30, 30),
					BorderSizePixel = 0,
					Position = UDim2.new(0.975, 0, 0.5, 0),
					Size = UDim2.new(0, 20, 0, 20)
				})

				New("UICorner", {
					CornerRadius = UDim.new(0.2, 0),
					Parent = Indicator
				})

				New("UIStroke", {
					Color = Color3.fromRGB(25, 25, 25),
					Thickness = 1,
					Parent = Indicator
				})

				local ClickButton = New("TextButton", {
					Parent = ToggleFrame,
					BackgroundTransparency = 1,
					Size = UDim2.new(1, 0, 1, 0),
					Text = "",
					AutoButtonColor = false
				})

				ClickButton.MouseButton1Click:Connect(function()
					element.Value = not element.Value

					TweenService:Create(Indicator, TweenInfo.new(0.2), {
						BackgroundColor3 = element.Value and Color3.fromRGB(230, 230, 230) or Color3.fromRGB(30, 30, 30)
					}):Play()

					if element.Callback then
						element.Callback(element.Value)
					end
				end)
			end
		end
	end

	function Window:Destroy()
		SeizureUI:Destroy()
		for i, win in pairs(Library.Windows) do
			if win == Window then
				table.remove(Library.Windows, i)
				break
			end
		end
	end

	table.insert(Library.Windows, Window)
	return Window
end

return Library
