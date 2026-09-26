--[[
    PAINEL MODERNO - STEAL A EGG (Roblox)
    LocalScript -> StarterPlayer -> StarterPlayerScripts
--]]

local Players          = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService     = game:GetService("TweenService")
local RunService       = game:GetService("RunService")
local Workspace        = game:GetService("Workspace")
local VirtualUser      = game:GetService("VirtualUser")

local LocalPlayer = Players.LocalPlayer
local PlayerGui   = LocalPlayer:WaitForChild("PlayerGui")

--==============================================================
-- CONFIGURAÇÕES
--==============================================================
local CONFIG = {
    Titulo          = "🥚 STEAL A EGG • PAINEL",
    TamanhoInicial  = UDim2.new(0, 560, 0, 380),
    CorFundo        = Color3.fromRGB(22, 24, 33),
    CorHeader       = Color3.fromRGB(30, 33, 48),
    CorAba          = Color3.fromRGB(38, 42, 60),
    CorAbaAtiva     = Color3.fromRGB(70, 120, 220),
    CorBotao        = Color3.fromRGB(45, 50, 70),
    CorBotaoAtivo   = Color3.fromRGB(60, 180, 100),
    CorBotaoInativo = Color3.fromRGB(200, 70, 70),
    CorTexto        = Color3.fromRGB(240, 240, 245),
    CorTextoSub     = Color3.fromRGB(160, 165, 180),
    Fonte           = Enum.Font.GothamMedium,
    FonteBold       = Enum.Font.GothamBold,
    RaioCanto       = UDim.new(0, 10),
}

local Estado = {
    AutoColetar      = false,
    ESPOvos          = false,
    AutoVender       = false,
    AntiAFK          = false,
    FarmAutomatico   = false,
    ColetaRapida     = false,
    AutoRebirth      = false,
    AutoUpgrade      = false,
    ModoRapido       = false,
    PularAlto        = false,
    AtravessarPortas = false,
    PrimeiraPessoa   = false,
    InfiniteJump     = false,
    Fly              = false,
    SpeedValue       = 32,
    JumpValue        = 60,
}

--==============================================================
-- UTILITÁRIOS
--==============================================================
local function aplicarCanto(inst, raio)
    local c = Instance.new("UICorner")
    c.CornerRadius = raio or CONFIG.RaioCanto
    c.Parent = inst
end

local function aplicarPadding(inst, px)
    local p = Instance.new("UIPadding")
    p.PaddingTop    = UDim.new(0, px)
    p.PaddingBottom = UDim.new(0, px)
    p.PaddingLeft   = UDim.new(0, px)
    p.PaddingRight  = UDim.new(0, px)
    p.Parent = inst
end

local function aplicarSombra(inst)
    local s = Instance.new("UIStroke")
    s.Color = Color3.fromRGB(0, 0, 0)
    s.Transparency = 0.6
    s.Thickness = 1
    s.Parent = inst
end

--==============================================================
-- GUI PRINCIPAL
--==============================================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "StealEggPainel"
ScreenGui.ResetOnSpawn = false
ScreenGui.IgnoreGuiInset = true
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Enabled = true
ScreenGui.Parent = PlayerGui

local Painel = Instance.new("Frame")
Painel.Name = "Painel"
Painel.Size = CONFIG.TamanhoInicial
Painel.Position = UDim2.new(0.5, -280, 0.5, -190)
Painel.BackgroundColor3 = CONFIG.CorFundo
Painel.BorderSizePixel = 0
Painel.ClipsDescendants = true
Painel.Active = true
Painel.Draggable = true -- fallback simples (funciona em mobile e PC)
Painel.Parent = ScreenGui
aplicarCanto(Painel)
aplicarSombra(Painel)

local Header = Instance.new("Frame")
Header.Name = "Header"
Header.Size = UDim2.new(1, 0, 0, 45)
Header.BackgroundColor3 = CONFIG.CorHeader
Header.BorderSizePixel = 0
Header.Parent = Painel

local HeaderTitulo = Instance.new("TextLabel")
HeaderTitulo.Size = UDim2.new(1, -100, 1, 0)
HeaderTitulo.Position = UDim2.new(0, 12, 0, 0)
HeaderTitulo.BackgroundTransparency = 1
HeaderTitulo.Text = CONFIG.Titulo
HeaderTitulo.TextColor3 = CONFIG.CorTexto
HeaderTitulo.Font = CONFIG.FonteBold
HeaderTitulo.TextSize = 16
HeaderTitulo.TextXAlignment = Enum.TextXAlignment.Left
HeaderTitulo.Parent = Header

local BtnMinimizar = Instance.new("TextButton")
BtnMinimizar.Size = UDim2.new(0, 30, 0, 30)
BtnMinimizar.Position = UDim2.new(1, -75, 0, 8)
BtnMinimizar.BackgroundColor3 = CONFIG.CorBotao
BtnMinimizar.Text = "—"
BtnMinimizar.TextColor3 = CONFIG.CorTexto
BtnMinimizar.Font = CONFIG.FonteBold
BtnMinimizar.TextSize = 16
BtnMinimizar.BorderSizePixel = 0
BtnMinimizar.Parent = Header
aplicarCanto(BtnMinimizar)

local BtnFechar = Instance.new("TextButton")
BtnFechar.Size = UDim2.new(0, 30, 0, 30)
BtnFechar.Position = UDim2.new(1, -40, 0, 8)
BtnFechar.BackgroundColor3 = Color3.fromRGB(200, 60, 60)
BtnFechar.Text = "✕"
BtnFechar.TextColor3 = CONFIG.CorTexto
BtnFechar.Font = CONFIG.FonteBold
BtnFechar.TextSize = 14
BtnFechar.BorderSizePixel = 0
BtnFechar.Parent = Header
aplicarCanto(BtnFechar)

-- Fechar / Minimizar
BtnFechar.MouseButton1Click:Connect(function()
    ScreenGui.Enabled = false
end)

local minimizado = false
BtnMinimizar.MouseButton1Click:Connect(function()
    minimizado = not minimizado
    if minimizado then
        TweenService:Create(Painel, TweenInfo.new(0.25), {
            Size = UDim2.new(0, 560, 0, 45)
        }):Play()
        ContentFrame.Visible = false
        TabsFrame.Visible = false
    else
        TweenService:Create(Painel, TweenInfo.new(0.25), {
            Size = CONFIG.TamanhoInicial
        }):Play()
        ContentFrame.Visible = true
        TabsFrame.Visible = true
    end
end)

local TabsFrame = Instance.new("Frame")
TabsFrame.Name = "Tabs"
TabsFrame.Size = UDim2.new(0, 140, 1, -45)
TabsFrame.Position = UDim2.new(0, 0, 0, 45)
TabsFrame.BackgroundColor3 = CONFIG.CorHeader
TabsFrame.BorderSizePixel = 0
TabsFrame.Parent = Painel

local TabsList = Instance.new("UIListLayout")
TabsList.Padding = UDim.new(0, 6)
TabsList.SortOrder = Enum.SortOrder.LayoutOrder
TabsList.Parent = TabsFrame
aplicarPadding(TabsFrame, 8)

local ContentFrame = Instance.new("Frame")
ContentFrame.Name = "Content"
ContentFrame.Size = UDim2.new(1, -140, 1, -45)
ContentFrame.Position = UDim2.new(0, 140, 0, 45)
ContentFrame.BackgroundTransparency = 1
ContentFrame.Parent = Painel

--==============================================================
-- SISTEMA DE PÁGINAS
--==============================================================
local Paginas = {}
local BotoesAbas = {}

local function criarPagina(nome, icone)
    local page = Instance.new("ScrollingFrame")
    page.Name = nome
    page.Size = UDim2.new(1, -16, 1, -16)
    page.Position = UDim2.new(0, 8, 0, 8)
    page.BackgroundTransparency = 1
    page.BorderSizePixel = 0
    page.ScrollBarThickness = 4
    page.ScrollBarImageColor3 = CONFIG.CorAbaAtiva
    page.CanvasSize = UDim2.new(0, 0, 0, 0)
    page.AutomaticCanvasSize = Enum.AutomaticSize.Y
    page.Visible = false
    page.Parent = ContentFrame

    local layout = Instance.new("UIListLayout")
    layout.Padding = UDim.new(0, 8)
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Parent = page

    local padding = Instance.new("UIPadding")
    padding.PaddingTop = UDim.new(0, 4)
    padding.PaddingRight = UDim.new(0, 8)
    padding.Parent = page

    Paginas[nome] = page

    local btn = Instance.new("TextButton")
    btn.Name = "Tab_" .. nome
    btn.Size = UDim2.new(1, 0, 0, 38)
    btn.BackgroundColor3 = CONFIG.CorAba
    btn.Text = "  " .. icone .. "  " .. nome
    btn.TextColor3 = CONFIG.CorTexto
    btn.Font = CONFIG.Fonte
    btn.TextSize = 13
    btn.TextXAlignment = Enum.TextXAlignment.Left
    btn.BorderSizePixel = 0
    btn.AutoButtonColor = false
    btn.Parent = TabsFrame
    aplicarCanto(btn)

    BotoesAbas[nome] = btn

    btn.MouseButton1Click:Connect(function()
        for nomeP, pagina in pairs(Paginas) do
            pagina.Visible = (nomeP == nome)
        end
        for nomeA, b in pairs(BotoesAbas) do
            TweenService:Create(b, TweenInfo.new(0.2), {
                BackgroundColor3 = (nomeA == nome) and CONFIG.CorAbaAtiva or CONFIG.CorAba
            }):Play()
        end
    end)

    return page
end

criarPagina("Principal", "🏠")
criarPagina("Ovos", "🥚")
criarPagina("Farm", "⚡")
criarPagina("Jogador", "👤")
criarPagina("Teleportes", "🌍")
criarPagina("Visual", "🎨")
criarPagina("Config", "⚙️")

Paginas["Principal"].Visible = true
BotoesAbas["Principal"].BackgroundColor3 = CONFIG.CorAbaAtiva

--==============================================================
-- NOTIFICAÇÃO
--==============================================================
local NotifFrame = Instance.new("Frame")
NotifFrame.Size = UDim2.new(0, 300, 0, 0)
NotifFrame.Position = UDim2.new(1, -320, 0, 20)
NotifFrame.BackgroundTransparency = 1
NotifFrame.Parent = ScreenGui

local NotifList = Instance.new("UIListLayout")
NotifList.Padding = UDim.new(0, 6)
NotifList.Parent = NotifFrame

local function notificar(texto, cor)
    cor = cor or CONFIG.CorAbaAtiva
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, 0, 0, 32)
    lbl.BackgroundColor3 = cor
    lbl.BackgroundTransparency = 0.2
    lbl.Text = "🔔  " .. texto
    lbl.TextColor3 = CONFIG.CorTexto
    lbl.Font = CONFIG.FonteBold
    lbl.TextSize = 13
    lbl.Parent = NotifFrame
    aplicarCanto(lbl)

    task.delay(3, function()
        TweenService:Create(lbl, TweenInfo.new(0.3), {BackgroundTransparency = 1, TextTransparency = 1}):Play()
        task.wait(0.35)
        lbl:Destroy()
    end)
end

--==============================================================
-- COMPONENTES DE UI
--==============================================================
local function criarToggle(parent, texto, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, 0, 0, 44)
    frame.BackgroundColor3 = CONFIG.CorBotao
    frame.BorderSizePixel = 0
    frame.Parent = parent
    aplicarCanto(frame)

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -80, 1, 0)
    label.Position = UDim2.new(0, 12, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = texto
    label.TextColor3 = CONFIG.CorTexto
    label.Font = CONFIG.Fonte
    label.TextSize = 14
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame

    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 60, 0, 28)
    btn.Position = UDim2.new(1, -70, 0.5, -14)
    btn.BackgroundColor3 = CONFIG.CorBotaoInativo
    btn.Text = "OFF"
    btn.TextColor3 = CONFIG.CorTexto
    btn.Font = CONFIG.FonteBold
    btn.TextSize = 12
    btn.BorderSizePixel = 0
    btn.Parent = frame
    aplicarCanto(btn)

    local estado = false
    btn.MouseButton1Click:Connect(function()
        estado = not estado
        TweenService:Create(btn, TweenInfo.new(0.2), {
            BackgroundColor3 = estado and CONFIG.CorBotaoAtivo or CONFIG.CorBotaoInativo
        }):Play()
        btn.Text = estado and "ON" or "OFF"
        if callback then callback(estado) end
    end)

    return frame, btn
end

local function criarBotao(parent, texto, callback, cor)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 0, 40)
    btn.BackgroundColor3 = cor or CONFIG.CorAbaAtiva
    btn.Text = texto
    btn.TextColor3 = CONFIG.CorTexto
    btn.Font = CONFIG.FonteBold
    btn.TextSize = 14
    btn.BorderSizePixel = 0
    btn.Parent = parent
    aplicarCanto(btn)

    btn.MouseButton1Click:Connect(function()
        if callback then callback() end
    end)
    return btn
end

local function criarSecao(parent, texto)
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, 0, 0, 28)
    lbl.BackgroundTransparency = 1
    lbl.Text = "▸ " .. texto
    lbl.TextColor3 = CONFIG.CorTextoSub
    lbl.Font = CONFIG.FonteBold
    lbl.TextSize = 12
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = parent
    return lbl
end

local function criarSlider(parent, texto, minV, maxV, valorInicial, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, 0, 0, 55)
    frame.BackgroundColor3 = CONFIG.CorBotao
    frame.BorderSizePixel = 0
    frame.Parent = parent
    aplicarCanto(frame)

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -20, 0, 22)
    label.Position = UDim2.new(0, 10, 0, 4)
    label.BackgroundTransparency = 1
    label.Text = texto .. ": " .. valorInicial
    label.TextColor3 = CONFIG.CorTexto
    label.Font = CONFIG.Fonte
    label.TextSize = 13
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame

    local barra = Instance.new("Frame")
    barra.Size = UDim2.new(1, -20, 0, 8)
    barra.Position = UDim2.new(0, 10, 0, 34)
    barra.BackgroundColor3 = CONFIG.CorAba
    barra.BorderSizePixel = 0
    barra.Parent = frame
    aplicarCanto(barra, UDim.new(1, 0))

    local preench = Instance.new("Frame")
    preench.Size = UDim2.new((valorInicial - minV)/(maxV - minV), 0, 1, 0)
    preench.BackgroundColor3 = CONFIG.CorAbaAtiva
    preench.BorderSizePixel = 0
    preench.Parent = barra
    aplicarCanto(preench, UDim.new(1, 0))

    local arrastando = false
    local function atualizar(input)
        local posX = math.clamp(input.Position.X - barra.AbsolutePosition.X, 0, barra.AbsoluteSize.X)
        local pct = posX / barra.AbsoluteSize.X
        local val = math.floor(minV + pct * (maxV - minV))
        preench.Size = UDim2.new(pct, 0, 1, 0)
        label.Text = texto .. ": " .. val
        if callback then callback(val) end
    end

    barra.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            arrastando = true
            atualizar(input)
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if arrastando and (input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch) then
            atualizar(input)
        end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            arrastando = false
        end
    end)

    return frame
end

--==============================================================
-- FUNÇÕES DO JOGO
--==============================================================
local PlayerChar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local Humanoid = PlayerChar:WaitForChild("Humanoid")
local RootPart = PlayerChar:WaitForChild("HumanoidRootPart")

LocalPlayer.CharacterAdded:Connect(function(char)
    PlayerChar = char
    Humanoid = char:WaitForChild("Humanoid")
    RootPart = char:WaitForChild("HumanoidRootPart")
end)

-- Anti AFK
LocalPlayer.Idled:Connect(function()
    if Estado.AntiAFK then
        pcall(function()
            VirtualUser:CaptureController()
            VirtualUser:ClickButton2(Vector2.new())
        end)
    end
end)

-- Encontra ovos
local function encontrarOvos()
    local ovos = {}
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("BasePart") then
            local nome = string.lower(obj.Name)
            if string.find(nome, "egg") or string.find(nome, "ovo") then
                table.insert(ovos, obj)
            end
        elseif obj:IsA("Model") then
            local nome = string.lower(obj.Name)
            if string.find(nome, "egg") or string.find(nome, "ovo") then
                local primary = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")
                if primary then table.insert(ovos, primary) end
            end
        end
    end
    return ovos
end

-- ESP
local ovosDestacados = {}
local function ligarESP()
    for _, ovo in ipairs(encontrarOvos()) do
        if not ovosDestacados[ovo] then
            local hl = Instance.new("Highlight")
            hl.FillColor = Color3.fromRGB(255, 215, 0)
            hl.OutlineColor = Color3.fromRGB(255, 255, 255)
            hl.FillTransparency = 0.5
            hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            hl.Parent = ovo
            ovosDestacados[ovo] = hl
        end
    end
end

local function desligarESP()
    for ovo, hl in pairs(ovosDestacados) do
        if hl and hl.Parent then hl:Destroy() end
        ovosDestacados[ovo] = nil
    end
end

RunService.RenderStepped:Connect(function()
    if Estado.ESPOvos then ligarESP() end
end)

-- Auto Coletar
task.spawn(function()
    while task.wait(1) do
        if Estado.AutoColetar and RootPart then
            local ovos = encontrarOvos()
            table.sort(ovos, function(a, b)
                return (a.Position - RootPart.Position).Magnitude < (b.Position - RootPart.Position).Magnitude
            end)
            local alvo = ovos[1]
            if alvo and (alvo.Position - RootPart.Position).Magnitude < 200 then
                pcall(function()
                    RootPart.CFrame = CFrame.new(alvo.Position + Vector3.new(0, 3, 0))
                end)
            end
        end
    end
end)

-- Farm
task.spawn(function()
    while task.wait(0.5) do
        if Estado.FarmAutomatico and RootPart then
            for _, ovo in ipairs(encontrarOvos()) do
                if (ovo.Position - RootPart.Position).Magnitude < 400 then
                    pcall(function()
                        RootPart.CFrame = CFrame.new(ovo.Position + Vector3.new(0, 2.5, 0))
                    end)
                    task.wait(0.1)
                end
            end
        end
    end
end)

-- Speed / Jump
RunService.Heartbeat:Connect(function()
    if Humanoid then
        if Estado.ModoRapido then
            Humanoid.WalkSpeed = Estado.SpeedValue
        else
            Humanoid.WalkSpeed = 16
        end
        if Estado.PularAlto then
            Humanoid.JumpPower = Estado.JumpValue
            Humanoid.UseJumpPower = true
        else
            Humanoid.JumpPower = 50
        end
    end
end)

-- Infinite Jump
UserInputService.JumpRequest:Connect(function()
    if Estado.InfiniteJump and Humanoid then
        Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

--==============================================================
-- CONSTRUÇÃO DAS PÁGINAS (BOTÕES)
--==============================================================

-- PRINCIPAL
criarSecao(Paginas["Principal"], "Geral")
criarToggle(Paginas["Principal"], "Anti-AFK", function(v)
    Estado.AntiAFK = v
    notificar("Anti-AFK: " .. (v and "ON" or "OFF"))
end)
criarToggle(Paginas["Principal"], "Auto Coletar", function(v)
    Estado.AutoColetar = v
    notificar("Auto Coletar: " .. (v and "ON" or "OFF"))
end)
criarToggle(Paginas["Principal"], "Farm Automático", function(v)
    Estado.FarmAutomatico = v
    notificar("Farm: " .. (v and "ON" or "OFF"))
end)
criarToggle(Paginas["Principal"], "ESP Ovos", function(v)
    Estado.ESPOvos = v
    if not v then desligarESP() end
    notificar("ESP: " .. (v and "ON" or "OFF"))
end)

-- OVOS
criarSecao(Paginas["Ovos"], "Ovos")
criarToggle(Paginas["Ovos"], "ESP Ovos", function(v)
    Estado.ESPOvos = v
    if not v then desligarESP() end
end)
criarToggle(Paginas["Ovos"], "Coleta Rápida", function(v) Estado.ColetaRapida = v end)
criarBotao(Paginas["Ovos"], "Teleportar para Ovo mais próximo", function()
    if RootPart then
        local ovos = encontrarOvos()
        table.sort(ovos, function(a, b)
            return (a.Position - RootPart.Position).Magnitude < (b.Position - RootPart.Position).Magnitude
        end)
        if ovos[1] then
            RootPart.CFrame = CFrame.new(ovos[1].Position + Vector3.new(0, 3, 0))
            notificar("Teleportado!")
        end
    end
end)

-- FARM
criarSecao(Paginas["Farm"], "Automação")
criarToggle(Paginas["Farm"], "Auto Rebirth", function(v) Estado.AutoRebirth = v end)
criarToggle(Paginas["Farm"], "Auto Upgrade", function(v) Estado.AutoUpgrade = v end)
criarToggle(Paginas["Farm"], "Auto Vender", function(v) Estado.AutoVender = v end)
criarToggle(Paginas["Farm"], "Farm Automático", function# Rere
