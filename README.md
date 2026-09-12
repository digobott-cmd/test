-- KS HUB SERVER SNIPER — AUTO HOP
-- Steal a Brainrot | Place ID: 109983668079237

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local TeleportService = game:GetService("TeleportService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- ============ CONFIG ============
local THRESHOLD = 10000000
local HOP_DELAY = 8
local MAX_HOPS = 50
local PLACE_ID = 109983668079237

local old = playerGui:FindFirstChild("KS_ServerSniper")
if old then old:Destroy() end

-- CORES
local BG = Color3.fromRGB(7,5,12)
local PANEL = Color3.fromRGB(14,10,24)
local PANEL2 = Color3.fromRGB(25,14,38)
local CARD = Color3.fromRGB(18,12,29)
local HOVER = Color3.fromRGB(30,17,47)
local PURPLE = Color3.fromRGB(174,75,255)
local PURPLE2 = Color3.fromRGB(112,38,190)
local PINK = Color3.fromRGB(236,82,255)
local TEXT = Color3.fromRGB(244,235,250)
local MUTED = Color3.fromRGB(157,139,170)
local GREEN = Color3.fromRGB(93,245,156)
local RED = Color3.fromRGB(255,90,90)
local LINE = Color3.fromRGB(68,38,86)

-- TABELA
local BRAINROTS = {
    ["la grande combinasion"] = {"La Grande Combinasion", 1000000000, "Secret"},
    ["garama and madundung"] = {"Garama and Madundung", 500000000, "Secret"},
    ["la vacca saturno saturnita"] = {"La Vacca Saturno Saturnita", 300000000, "Secret"},
    ["la vacca astral"] = {"La Vacca Astral", 750000000, "Secret"},
    ["nuclearo dinossauro"] = {"Nuclearo Dinossauro", 150000000, "Secret"},
    ["secret lucky"] = {"Secret Lucky", 2500000000, "Secret"},
    ["las tralaleritas"] = {"Las Tralaleritas", 500000000, "Secret"},
    ["coco elefanto"] = {"Coco Elefanto", 5000000, "God"},
    ["cocofanto elefante"] = {"Coco Elefanto", 5000000, "God"},
    ["girafa celeste"] = {"Girafa Celeste", 3000000, "God"},
    ["bombardiro crocodilo"] = {"Bombardiro Crocodilo", 500000, "Mythic"},
    ["blueberrini octopusini"] = {"Blueberrini Octopusini", 400000, "Mythic"},
    ["chihuaini tacorini"] = {"Chihuaini Tacorini", 350000, "Mythic"},
    ["toletto focaccino"] = {"Toletto Focaccino", 450000, "Mythic"},
    ["urubini flamenguini"] = {"Urubini Flamenguini", 300000, "Mythic"},
    ["beavo potto"] = {"Beavo Potto", 250000, "Mythic"},
    ["tralalero tralala"] = {"Tralalero Tralala", 100000, "Legendary"},
    ["ballerina cappuccina"] = {"Ballerina Cappuccina", 100000, "Legendary"},
    ["gelatina volatina"] = {"Gelatina Volatina", 80000, "Legendary"},
    ["tic tic ribbit"] = {"Tic Tic Ribbit", 60000, "Legendary"},
    ["cappuccino assassino"] = {"Cappuccino Assassino", 10000, "Epic"},
    ["abelha rainha"] = {"Abelha Rainha", 5000, "Epic"},
    ["trippi troppi"] = {"Trippi Troppi", 2000, "Rare"},
    ["noobini pizzanini"] = {"Noobini Pizzanini", 25, "Common"},
    ["lirili larila"] = {"Lirili Larila", 250, "Common"},
}

-- HELPERS
local function tw(o,t,p,style)
    if not o then return end
    TweenService:Create(o,TweenInfo.new(t or .2,style or Enum.EasingStyle.Quint,Enum.EasingDirection.Out),p):Play()
end
local function cr(o,r)
    local c=Instance.new("UICorner"); c.CornerRadius=UDim.new(0,r or 10); c.Parent=o
end
local function st(o,c,w)
    local s=Instance.new("UIStroke"); s.Color=c; s.Thickness=w or 1; s.Transparency=.12; s.Parent=o
end
local function txt(p,s,z,pos,font,size)
    local l=Instance.new("TextLabel")
    l.BackgroundTransparency=1;l.Size=z;l.Position=pos;l.Text=s
    l.TextColor3=TEXT;l.Font=font or Enum.Font.Gotham;l.TextSize=size or 12
    l.TextXAlignment=Enum.TextXAlignment.Left;l.TextYAlignment=Enum.TextYAlignment.Center;l.Parent=p
    return l
end
local function btn(p,s,z,pos)
    local b=Instance.new("TextButton")
    b.BackgroundColor3=PANEL2;b.BorderSizePixel=0;b.Size=z;b.Position=pos;b.Text=s
    b.TextColor3=TEXT;b.Font=Enum.Font.GothamBold;b.TextSize=12;b.AutoButtonColor=false;b.Parent=p
    cr(b,9);st(b,LINE,1);return b
end

local function fmtValor(v)
    if v >= 1000000000 then return "$"..string.format("%.2fB", v/1000000000)
    elseif v >= 1000000 then return "$"..string.format("%.2fM", v/1000000)
    elseif v >= 1000 then return "$"..string.format("%.1fK", v/1000)
    else return "$"..tostring(v) end
end

-- SCAN
local function scanBrainrots()
    local achados = {}
    local function getPos(m)
        if not m then return nil end
        if m:IsA("Model") and m.PrimaryPart then
            return m.PrimaryPart.Position
        elseif m:IsA("Model") then
            local ok, p = pcall(function() return m:GetPivot().Position end)
            if ok then return p end
        elseif m:IsA("BasePart") then
            return m.Position
        end
        return nil
    end
    local function match(nome)
        local nomeLower = string.lower(nome)
        if BRAINROTS[nomeLower] then return BRAINROTS[nomeLower] end
        for key, info in pairs(BRAINROTS) do
            if string.find(nomeLower, key, 1, true) then
                return info
            end
        end
        return nil
    end
    local function verificar(m)
        local info = match(m.Name)
        if not info then return end
        local pos = getPos(m)
        if not pos then return end
        table.insert(achados, {nome = info[1], valor = info[2], raridade = info[3], posicao = pos, obj = m})
    end
    local plots = workspace:FindFirstChild("Plots")
    if plots then
        for _, plot in ipairs(plots:GetChildren()) do
            for _, filho in ipairs(plot:GetChildren()) do
                if filho:IsA("Model") then
                    verificar(filho)
                end
            end
        end
    end
    table.sort(achados, function(a,b) return a.valor > b.valor end)
    return achados
end

-- ============ UI ============
local gui=Instance.new("ScreenGui")
gui.Name="KS_ServerSniper"
gui.ResetOnSpawn=false
gui.IgnoreGuiInset=true
gui.ZIndexBehavior=Enum.ZIndexBehavior.Sibling
gui.Parent=playerGui

local shadow=Instance.new("Frame")
shadow.Size=UDim2.fromOffset(700,500)
shadow.Position=UDim2.new(.5,-346,.5,-244)
shadow.BackgroundColor3=Color3.fromRGB(74,17,101)
shadow.BackgroundTransparency=.76
shadow.BorderSizePixel=0
shadow.ZIndex=1
shadow.Parent=gui
cr(shadow,18)

local main=Instance.new("Frame")
main.Size=UDim2.fromOffset(680,480)
main.Position=UDim2.new(.5,-340,.5,-240)
main.BackgroundColor3=BG
main.BorderSizePixel=0
main.ZIndex=2
main.Parent=gui
cr(main,16)
st(main,Color3.fromRGB(109,49,139),1)

local header=Instance.new("Frame")
header.Size=UDim2.new(1,-30,0,55)
header.Position=UDim2.fromOffset(15,3)
header.BackgroundTransparency=1
header.Parent=main

local logo=Instance.new("Frame")
logo.Size=UDim2.fromOffset(39,39)
logo.Position=UDim2.fromOffset(0,7)
logo.BackgroundColor3=PURPLE2
logo.BorderSizePixel=0
logo.Parent=header
cr(logo,11)

local lt=txt(logo,"KS",UDim2.fromScale(1,1),UDim2.fromScale(0,0),Enum.Font.GothamBlack,13)
lt.TextXAlignment=Enum.TextXAlignment.Center

txt(header,"SERVER SNIPER",UDim2.fromOffset(300,25),UDim2.fromOffset(51,5),Enum.Font.GothamBlack,18)
local sub=txt(header,"Auto hop até achar 10M+",UDim2.fromOffset(350,18),UDim2.fromOffset(51,29),Enum.Font.Gotham,10)
sub.TextColor3=MUTED

local close=btn(header,"×",UDim2.fromOffset(34,32),UDim2.new(1,-34,0,7))
close.TextSize=21

local accent=Instance.new("Frame")
accent.Size=UDim2.new(1,-30,0,2)
accent.Position=UDim2.fromOffset(15,55)
accent.BackgroundColor3=PURPLE
accent.BorderSizePixel=0
accent.Parent=main

task.spawn(function()
    while accent.Parent do
        tw(accent,1.4,{BackgroundColor3=PINK},Enum.EasingStyle.Sine)
        task.wait(1.4)
        tw(accent,1.4,{BackgroundColor3=PURPLE},Enum.EasingStyle.Sine)
        task.wait(1.4)
    end
end)

local left=Instance.new("Frame")
left.Size=UDim2.fromOffset(155,397)
left.Position=UDim2.fromOffset(15,68)
left.BackgroundColor3=PANEL
left.BorderSizePixel=0
left.Parent=main
cr(left,12)
st(left,LINE,1)

local right=Instance.new("Frame")
right.Size=UDim2.fromOffset(495,397)
right.Position=UDim2.fromOffset(180,68)
right.BackgroundTransparency=1
right.Parent=main

local k=txt(left,"KS HUB",UDim2.new(1,-20,0,18),UDim2.fromOffset(10,11),Enum.Font.GothamBlack,11)
k.TextColor3=PURPLE

txt(left,"SERVER SNIPER",UDim2.new(1,-20,0,25),UDim2.fromOffset(10,32),Enum.Font.GothamBold,13)
local tm=txt(left,"● AUTO MODE",UDim2.new(1,-20,0,18),UDim2.fromOffset(10,57),Enum.Font.GothamBold,9)
tm.TextColor3=GREEN

local refresh=btn(left,"⟳  SCAN",UDim2.new(1,-20,0,38),UDim2.fromOffset(10,88))
local hopBtn=btn(left,"⚡  SERVER HOP",UDim2.new(1,-20,0,38),UDim2.fromOffset(10,131))
local autoBtn=btn(left,"🤖  AUTO SNIPER",UDim2.new(1,-20,0,38),UDim2.fromOffset(10,174))

local sb=Instance.new("Frame")
sb.Size=UDim2.new(1,-20,0,67)
sb.Position=UDim2.fromOffset(10,222)
sb.BackgroundColor3=CARD
sb.BorderSizePixel=0
sb.Parent=left
cr(sb,9)
st(sb,LINE,1)

txt(sb,"STATUS",UDim2.new(1,-16,0,15),UDim2.fromOffset(8,6),Enum.Font.GothamBold,9).TextColor3=MUTED
local status=txt(sb,"READY",UDim2.new(1,-16,0,22),UDim2.fromOffset(8,23),Enum.Font.GothamBlack,11)
status.TextColor3=GREEN
local found=txt(sb,"0 brainrots",UDim2.new(1,-16,0,15),UDim2.fromOffset(8,45),Enum.Font.Gotham,9)
found.TextColor3=MUTED

local bm=Instance.new("Frame")
bm.Size=UDim2.new(1,-20,0,88)
bm.Position=UDim2.fromOffset(10,298)
bm.BackgroundColor3=CARD
bm.BorderSizePixel=0
bm.Parent=left
cr(bm,9)
st(bm,Color3.fromRGB(113,55,142),1)

local bl=txt(bm,"🏆  BEST FIND",UDim2.new(1,-16,0,17),UDim2.fromOffset(8,6),Enum.Font.GothamBlack,10)
bl.TextColor3=PURPLE

local bestName=txt(bm,"—",UDim2.new(1,-16,0,22),UDim2.fromOffset(8,28),Enum.Font.GothamBold,11)
local bestVal=txt(bm,"—",UDim2.new(1,-16,0,22),UDim2.fromOffset(8,50),Enum.Font.GothamBlack,13)
bestVal.TextColor3=GREEN
local bestServer=txt(bm,"—",UDim2.new(1,-16,0,16),UDim2.fromOffset(8,70),Enum.Font.Gotham,9)
bestServer.TextColor3=MUTED

local ft=txt(left,"AUTO • LIMITE $"..(THRESHOLD/1000000).."M",UDim2.new(1,-20,0,15),UDim2.fromOffset(10,373),Enum.Font.GothamBold,8)
ft.TextColor3=Color3.fromRGB(104,82,116)

txt(right,"BRAINROTS DETECTADOS",UDim2.fromOffset(300,24),UDim2.fromOffset(3,0),Enum.Font.GothamBlack,14)
local count=txt(right,"0 ITENS",UDim2.fromOffset(150,20),UDim2.new(1,-153,0,1),Enum.Font.GothamBold,9)
count.TextXAlignment=Enum.TextXAlignment.Right
count.TextColor3=MUTED

local scroll=Instance.new("ScrollingFrame")
scroll.Size=UDim2.new(1,0,1,-36)
scroll.Position=UDim2.fromOffset(0,32)
scroll.BackgroundTransparency=1
scroll.BorderSizePixel=0
scroll.ScrollBarThickness=3
scroll.ScrollBarImageColor3=PURPLE2
scroll.AutomaticCanvasSize=Enum.AutomaticSize.Y
scroll.Parent=right

local layout=Instance.new("UIListLayout")
layout.Padding=UDim.new(0,9)
layout.Parent=scroll

local function render(lista)
    for _,x in ipairs(scroll:GetChildren()) do
        if x:IsA("Frame") or x:IsA("TextLabel") then x:Destroy() end
    end
    count.Text = #lista.." ITENS"

    if #lista == 0 then
        local vazio = Instance.new("TextLabel")
        vazio.Size = UDim2.new(1,-5,0,80)
        vazio.BackgroundTransparency = 1
        vazio.Text = "Nenhum Brainrot detectado.\n\nUse SERVER HOP ou AUTO SNIPER."
        vazio.TextColor3 = MUTED
        vazio.Font = Enum.Font.Gotham
        vazio.TextSize = 12
        vazio.TextWrapped = true
        vazio.Parent = scroll
        return
    end

    for i, b in ipairs(lista) do
        local c=Instance.new("Frame")
        c.Size=UDim2.new(1,-5,0,38)
        c.BackgroundColor3=CARD
        c.BorderSizePixel=0
        c.LayoutOrder=i
        c.Parent=scroll
        cr(c,7)

        local dot=Instance.new("Frame")
        dot.Size=UDim2.fromOffset(6,6)
        dot.Position=UDim2.fromOffset(9,16)
        dot.BackgroundColor3=PURPLE
        dot.BorderSizePixel=0
        dot.Parent=c
        cr(dot,6)

        if b.valor >= THRESHOLD then
            dot.BackgroundColor3 = GREEN
            c.BackgroundColor3 = Color3.fromRGB(25, 45, 30)
            st(c, GREEN, 1)
        end

        txt(c,b.nome,UDim2.new(0,240,1,0),UDim2.fromOffset(22,0),Enum.Font.GothamSemibold,11)
        local v=txt(c,fmtValor(b.valor),UDim2.fromOffset(90,38),UDim2.new(0,270,0,0),Enum.Font.GothamBold,10)
        v.TextColor3=GREEN
        v.TextXAlignment=Enum.TextXAlignment.Right

        local r=txt(c,b.raridade,UDim2.fromOffset(80,38),UDim2.new(1,-88,0,0),Enum.Font.GothamBold,9)
        r.TextColor3 = PURPLE
        r.TextXAlignment=Enum.TextXAlignment.Right
    end
end

-- BOTÃO SCAN
local busy = false
refresh.Activated:Connect(function()
    if busy then return end
    busy = true
    refresh.Text = "◌  SCANNING..."
    status.Text = "SCANNING"
    status.TextColor3 = PURPLE
    tw(refresh,.15,{BackgroundColor3=PURPLE2})
    task.spawn(function()
        local lista = scanBrainrots()
        render(lista)
        found.Text = #lista.." brainrots"
        if #lista > 0 then
            local top = lista[1]
            bestName.Text = top.nome
            bestVal.Text = fmtValor(top.valor).." • "..top.raridade
            bestServer.Text = "Server: "..game.JobId:sub(1,8)
            status.Text = "FOUND "..#lista
            status.TextColor3 = GREEN
        else
            bestName.Text = "—"
            bestVal.Text = "—"
            bestServer.Text = "—"
            status.Text = "NONE"
            status.TextColor3 = RED
        end
        refresh.Text = "⟳  SCAN"
        tw(refresh,.2,{BackgroundColor3=PANEL2})
        busy = false
    end)
end)

-- ============ BOTÃO SERVER HOP (IGUAL AO TESTE QUE FUNCIONOU) ============
local hopEmAndamento = false

hopBtn.Activated:Connect(function()
    if hopEmAndamento then return end
    hopEmAndamento = true

    hopBtn.Text = "◌  HOPPING..."
    status.Text = "HOPPING..."
    status.TextColor3 = PURPLE

    task.spawn(function()
        local ok, err = pcall(function()
            TeleportService:Teleport(PLACE_ID, player)
        end)

        if ok then
            status.Text = "TELEPORT OK"
            status.TextColor3 = GREEN
        else
            status.Text = "ERRO: " .. tostring(err)
            status.TextColor3 = RED
            hopBtn.Text = "⚡  SERVER HOP"
            tw(hopBtn, .2, {BackgroundColor3 = PANEL2})
            hopEmAndamento = false
        end
    end)
end)

-- ============ AUTO SNIPER ============
local autoAtivo = false
local hopsFeitos = 0

local function iniciarAuto()
    autoAtivo = true
    autoBtn.Text = "🤖  AUTO ON"
    tw(autoBtn, .15, {BackgroundColor3 = PURPLE2})
    hopsFeitos = 0

    task.spawn(function()
        task.wait(8)
        while autoAtivo do
            status.Text = "ESCANEANDO..."
            status.TextColor3 = PURPLE
            local lista = scanBrainrots()
            render(lista)
            found.Text = #lista.." brainrots"
            if #lista > 0 then
                local top = lista[1]
                bestName.Text = top.nome
                bestVal.Text = fmtValor(top.valor).." • "..top.raridade
                bestServer.Text = "Server: "..game.JobId:sub(1,8)
            end
            local achouBom = false
            for _, b in ipairs(lista) do
                if b.valor >= THRESHOLD then
                    achouBom = true
                    break
                end
            end
            if achouBom then
                status.Text = "✅ ACHOU $"..(THRESHOLD/1000000).."M+"
                status.TextColor3 = GREEN
                autoBtn.Text = "🤖  ACHOU!"
                tw(autoBtn, .3, {BackgroundColor3 = GREEN})
                game:GetService("StarterGui"):SetCore("SendNotification", {
                    Title = "KS SNIPER",
                    Text = "Brainrot de $"..(THRESHOLD/1000000).."M+ encontrado!",
                    Duration = 8
                })
                autoAtivo = false
                break
            end
            hopsFeitos = hopsFeitos + 1
            if hopsFeitos >= MAX_HOPS then
                status.Text = "LIMITE DE HOPS"
                status.TextColor3 = RED
                autoAtivo = false
                autoBtn.Text = "🤖  AUTO SNIPER"
                tw(autoBtn, .15, {BackgroundColor3 = PANEL2})
                break
            end
            status.Text = "HOP "..hopsFeitos.."/"..MAX_HOPS
            status.TextColor3 = PURPLE
            task.wait(HOP_DELAY)

            local ok, err = pcall(function()
                TeleportService:Teleport(PLACE_ID, player)
            end)
            if not ok then
                warn("[KS] Auto hop erro: "..tostring(err))
            end
            task.wait(5)
        end
    end)
end

local function pararAuto()
    autoAtivo = false
    autoBtn.Text = "🤖  AUTO SNIPER"
    tw(autoBtn, .15, {BackgroundColor3 = PANEL2})
    status.Text = "PARADO"
    status.TextColor3 = MUTED
end

autoBtn.Activated:Connect(function()
    if autoAtivo then pararAuto() else iniciarAuto() end
end)

-- DRAG
local dragging=false
local dragStart
local startPos

header.InputBegan:Connect(function(input)
    if input.UserInputType==Enum.UserInputType.MouseButton1 or input.UserInputType==Enum.UserInputType.Touch then
        dragging=true
        dragStart=input.Position
        startPos=main.Position
        input.Changed:Connect(function()
            if input.UserInputState==Enum.UserInputState.End then dragging=false end
        end)
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if not dragging then return end
    if input.UserInputType~=Enum.UserInputType.MouseMovement and input.UserInputType~=Enum.UserInputType.Touch then return end
    local d=input.Position-dragStart
    local p=UDim2.new(startPos.X.Scale,startPos.X.Offset+d.X,startPos.Y.Scale,startPos.Y.Offset+d.Y)
    main.Position=p
    shadow.Position=UDim2.new(p.X.Scale,p.X.Offset+6,p.Y.Scale,p.Y.Offset+6)
end)

-- CLOSE
close.Activated:Connect(function()
    tw(main,.28,{Size=UDim2.fromOffset(0,0),BackgroundTransparency=1})
    tw(shadow,.28,{Size=UDim2.fromOffset(0,0),BackgroundTransparency=1})
    task.delay(.3,function() gui.Enabled=false end)
end)

UserInputService.InputBegan:Connect(function(input,processed)
    if processed then return end
    if input.KeyCode==Enum.KeyCode.RightShift and not gui.Enabled then
        gui.Enabled=true
        main.Size=UDim2.fromOffset(0,0)
        shadow.Size=UDim2.fromOffset(0,0)
        main.BackgroundTransparency=1
        shadow.BackgroundTransparency=1
        tw(main,.3,{Size=UDim2.fromOffset(680,480),BackgroundTransparency=0})
        tw(shadow,.3,{Size=UDim2.fromOffset(700,500),BackgroundTransparency=.76})
    end
end)

main.Size=UDim2.fromOffset(0,0)
shadow.Size=UDim2.fromOffset(0,0)
main.BackgroundTransparency=1
shadow.BackgroundTransparency=1
task.wait(.08)
tw(main,.42,{Size=UDim2.fromOffset(680,480),BackgroundTransparency=0})
tw(shadow,.42,{Size=UDim2.fromOffset(700,500),BackgroundTransparency=.76})
task.wait(.3)
