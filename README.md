-- DRAKE MENU - VERSÃO FINAL CORRIGIDA
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")

local player = Players.LocalPlayer
local pgui = player:WaitForChild("PlayerGui")

-- 1. SCREEN GUI
local sg = Instance.new("ScreenGui")
sg.Name = "DrakeMenuGui"
sg.ResetOnSpawn = false
sg.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
sg.Parent = pgui

-- FUNÇÃO DRAGGABLE MELHORADA (Funciona mesmo com botões em cima)
local function makeDraggable(gui, handle)
    handle = handle or gui
    local dragging, dragInput, dragStart, startPos
    handle.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = gui.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then dragging = false end
            end)
        end
    end)
    handle.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then dragInput = input end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            gui.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end

-- 2. BOTÃO DE ABRIR (IGUAL À FOTO)
local openBtn = Instance.new("Frame")
openBtn.Name = "MinimizedPill"
openBtn.Size = UDim2.new(0, 220, 0, 45)
openBtn.Position = UDim2.new(0.5, -110, 0, 60)
openBtn.BackgroundColor3 = Color3.fromRGB(30, 20, 35)
openBtn.Active = true
openBtn.Parent = sg
Instance.new("UICorner", openBtn).CornerRadius = UDim.new(1, 0)

local btnStroke = Instance.new("UIStroke")
btnStroke.Color = Color3.fromRGB(150, 0, 255)
btnStroke.Thickness = 2.5
btnStroke.Parent = openBtn

local btnText = Instance.new("TextButton")
btnText.Size = UDim2.new(1, 0, 1, 0)
btnText.BackgroundTransparency = 1
btnText.Text = "Drake Menu"
btnText.TextColor3 = Color3.fromRGB(255, 255, 255)
btnText.Font = Enum.Font.GothamBold
btnText.TextSize = 17
btnText.Parent = openBtn

-- ATIVA O MOVIMENTO NO BOTÃO (Usando o btnText como alça)
makeDraggable(openBtn, btnText)

-- 3. FRAME PRINCIPAL (DESIGN ORIGINAL)
local main = Instance.new("Frame")
main.Name = "MainFrame"
main.Size = UDim2.new(0, 580, 0, 380)
main.Position = UDim2.new(0.5, -290, 0.5, -190)
main.BackgroundColor3 = Color3.fromRGB(20, 10, 25)
main.BackgroundTransparency = 0.1
main.Visible = false
main.Active = true
main.Parent = sg
Instance.new("UICorner", main).CornerRadius = UDim.new(0, 15)

-- BARRA DE TOPO (Para arrastar o menu e fechar)
local topBar = Instance.new("Frame")
topBar.Size = UDim2.new(1, 0, 0, 45)
topBar.BackgroundTransparency = 1
topBar.Parent = main
makeDraggable(main, topBar) -- Arrastar pela barra de topo

local topTitle = Instance.new("TextLabel")
topTitle.Size = UDim2.new(1, -50, 1, 0)
topTitle.Position = UDim2.new(0, 20, 0, 0)
topTitle.Text = "DRAKE MENU"
topTitle.TextColor3 = Color3.fromRGB(200, 150, 255)
topTitle.Font = Enum.Font.GothamBold
topTitle.TextSize = 16
topTitle.TextXAlignment = Enum.TextXAlignment.Left
topTitle.BackgroundTransparency = 1
topTitle.Parent = topBar

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 35, 0, 35)
closeBtn.Position = UDim2.new(1, -40, 0, 5)
closeBtn.Text = "×"
closeBtn.TextSize = 32
closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
closeBtn.BackgroundTransparency = 1
closeBtn.Parent = topBar

-- 4. SIDEBAR COM ROLAGEM (PARA O BOTÃO HOME)
local sideBarFrame = Instance.new("Frame")
sideBarFrame.Size = UDim2.new(0, 170, 1, -45)
sideBarFrame.Position = UDim2.new(0, 0, 0, 45)
sideBarFrame.BackgroundTransparency = 1
sideBarFrame.Parent = main

local sideScroll = Instance.new("ScrollingFrame")
sideScroll.Size = UDim2.new(1, -10, 1, -10)
sideScroll.Position = UDim2.new(0, 5, 0, 5)
sideScroll.BackgroundTransparency = 1
sideScroll.ScrollBarThickness = 2
sideScroll.ScrollBarImageColor3 = Color3.fromRGB(150, 0, 255)
sideScroll.CanvasSize = UDim2.new(0, 0, 1.5, 0) 
sideScroll.Parent = sideBarFrame

local sideList = Instance.new("UIListLayout")
sideList.Padding = UDim.new(0, 8)
sideList.HorizontalAlignment = Enum.HorizontalAlignment.Center
sideList.Parent = sideScroll

local homeBtn = Instance.new("TextButton")
homeBtn.Size = UDim2.new(0.9, 0, 0, 40)
homeBtn.BackgroundColor3 = Color3.fromRGB(150, 0, 255)
homeBtn.BackgroundTransparency = 0.8
homeBtn.Text = "  🏠 Home"
homeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
homeBtn.Font = Enum.Font.GothamBold
homeBtn.TextXAlignment = Enum.TextXAlignment.Left
homeBtn.Parent = sideScroll
Instance.new("UICorner", homeBtn)

-- CONTAINER DE OPÇÕES (DIREITA)
local container = Instance.new("ScrollingFrame")
container.Size = UDim2.new(1, -190, 1, -60)
container.Position = UDim2.new(0, 180, 0, 50)
container.BackgroundTransparency = 1
container.ScrollBarThickness = 0
container.Parent = main
Instance.new("UIListLayout", container).Padding = UDim.new(0, 15)

-- FUNÇÃO TOGGLE (LETRAS PEQUENAS MAIORES)
local function createToggle(name, desc, callback)
	local tFrame = Instance.new("Frame")
	tFrame.Size = UDim2.new(1, -10, 0, 95)
	tFrame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
	tFrame.BackgroundTransparency = 0.96
	tFrame.Parent = container
	Instance.new("UICorner", tFrame)

	local tName = Instance.new("TextLabel")
	tName.Text = name
	tName.Size = UDim2.new(1, -70, 0, 35)
	tName.Position = UDim2.new(0, 15, 0, 10)
	tName.TextColor3 = Color3.fromRGB(255, 255, 255)
	tName.Font = Enum.Font.GothamBold
	tName.TextSize = 20
	tName.BackgroundTransparency = 1
	tName.TextXAlignment = Enum.TextXAlignment.Left
	tName.Parent = tFrame

	local tDesc = Instance.new("TextLabel")
	tDesc.Text = desc
	tDesc.Size = UDim2.new(1, -80, 0, 45)
	tDesc.Position = UDim2.new(0, 15, 0, 40)
	tDesc.TextColor3 = Color3.fromRGB(200, 200, 200)
	tDesc.TextSize = 15 
	tDesc.TextWrapped = true
	tDesc.BackgroundTransparency = 1
	tDesc.TextXAlignment = Enum.TextXAlignment.Left
	tDesc.Parent = tFrame

	local switch = Instance.new("TextButton")
	switch.Size = UDim2.new(0, 50, 0, 26)
	switch.Position = UDim2.new(1, -65, 0.5, -13)
	switch.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
	switch.Text = ""
	switch.Parent = tFrame
	Instance.new("UICorner", switch).CornerRadius = UDim.new(1, 0)

	local dot = Instance.new("Frame")
	dot.Size = UDim2.new(0, 22, 0, 22)
	dot.Position = UDim2.new(0, 2, 0, 2)
	dot.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
	dot.Parent = switch
	Instance.new("UICorner", dot).CornerRadius = UDim.new(1, 0)

	local active = false
	switch.MouseButton1Click:Connect(function()
		active = not active
		TweenService:Create(dot, TweenInfo.new(0.2), {Position = active and UDim2.new(1, -24, 0, 2) or UDim2.new(0, 2, 0, 2)}):Play()
		switch.BackgroundColor3 = active and Color3.fromRGB(150, 0, 255) or Color3.fromRGB(60, 60, 60)
		callback(active)
	end)
end

-- 5. LÓGICA GOD MODE REVERSÍVEL
local godParts = {}
local storedWalls = {}

local function spawnGod(name, pos, size)
	local p = Instance.new("Part")
	p.Name = name
	p.Position = pos
	p.Size = size
	p.Anchored = true
	p.CanCollide = true
	p.Transparency = 0.3
	p.Material = Enum.Material.ForceField
	p.Color = Color3.fromRGB(150, 0, 255)
	p.Parent = Workspace
	table.insert(godParts, p)
end

createToggle("God Mode", "Create protective walls and ground zones", function(state)
	local defaultMap = Workspace:FindFirstChild("DefaultMap")
	if state then
		-- Esconde as paredes originais
		if defaultMap then
			for _, wallName in pairs({"Walls", "RightWalls"}) do
				local wall = defaultMap:FindFirstChild(wallName)
				if wall then
					wall.Parent = nil 
					storedWalls[wallName] = wall
				end
			end
		end
		-- Cria as partes conforme as fotos
		spawnGod("GodMode1", Vector3.new(1890, -2.977, -137), Vector3.new(2048, 6, 15))
		spawnGod("GodMode2", Vector3.new(500, -2.976, -137), Vector3.new(800, 6, 15))
		spawnGod("GodMode3", Vector3.new(520, 21.524, -142), Vector3.new(800, 55, 1))
		spawnGod("GodMode4", Vector3.new(1890, 21.524, -142), Vector3.new(2048, 55, 1))
	else
		-- Remove GodMode 1, 2, 3 e 4
		for _, v in pairs(godParts) do v:Destroy() end
		godParts = {}
		-- Faz as paredes voltarem
		if defaultMap then
			for wallName, wallInstance in pairs(storedWalls) do
				wallInstance.Parent = defaultMap
			end
		end
		storedWalls = {}
	end
end)

createToggle("Auto Delete VIP Walls", "Automatically delete only VIP walls", function(state)
	if state then
		local shared = Workspace:FindFirstChild("DefaultMap_SharedInstances")
		if shared and shared:FindFirstChild("VIPWalls") then shared.VIPWalls:Destroy() end
	end
end)

-- CONTROLES DE ABRIR/FECHAR
btnText.MouseButton1Click:Connect(function()
    -- Só abre o menu se NÃO estiver arrastando (para evitar abrir sem querer ao mover)
    main.Visible = true
    openBtn.Visible = false
end)

closeBtn.MouseButton1Click:Connect(function()
	main.Visible = false
	openBtn.Visible = true
end)

