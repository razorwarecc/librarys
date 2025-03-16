local HttpService = game:GetService("HttpService")
local Typeface = {}
local fontCache = {}

local function ensureDirectories(basePath, folders)
    for _, path in next, folders do
        if not isfolder(basePath .. path) then
            makefolder(basePath .. path)
        end
    end
end

local function ensureFile(filePath, url)
    if not isfile(filePath) then
        local success, result = pcall(function()
            return game:HttpGet(url)
        end)
        
        if success then
            writefile(filePath, result)
            return true
        else
            warn("Failed to download file from " .. url .. ": " .. tostring(result))
            return false
        end
    end
    return true
end

function Typeface:Register(baseDirectory, fontInfo)
    assert(type(baseDirectory) == "string", "Base directory must be a string")
    assert(type(fontInfo) == "table", "Font info must be a table")
    assert(fontInfo.name, "Font must have a name")
    assert(fontInfo.link, "Font must have a download link")
    
    fontInfo.weight = fontInfo.weight or "Regular"
    fontInfo.style = fontInfo.style or "Normal"
    
    if not isfolder(baseDirectory) then
        makefolder(baseDirectory)
    end
    
    local fontsDir = baseDirectory .. "/fonts"
    if not isfolder(fontsDir) then
        makefolder(fontsDir)
    end
    
    local fontName = fontInfo.name:gsub("%s+", "_"):lower()
    local rawFontPath = fontsDir .. "/" .. fontName .. ".ttf"
    local encodedFontPath = fontsDir .. "/" .. fontName .. "_encoded.ttf"
    
    if not ensureFile(rawFontPath, fontInfo.link) then
        return nil
    end
    
    local fontDescriptor = {
        name = fontInfo.name,
        faces = {
            {
                name = fontInfo.weight,
                weight = fontInfo.weight == "Bold" and 700 or 400,
                style = fontInfo.style:lower(),
                assetId = getcustomasset(rawFontPath)
            }
        }
    }
    
    writefile(encodedFontPath, HttpService:JSONEncode(fontDescriptor))
    
    local weightEnum = Enum.FontWeight.Regular
    if fontInfo.weight == "Bold" then
        weightEnum = Enum.FontWeight.Bold
    elseif fontInfo.weight == "Light" then
        weightEnum = Enum.FontWeight.Light
    end
    
    local cacheKey = fontInfo.name .. "-" .. fontInfo.weight .. "-" .. fontInfo.style
    if not fontCache[cacheKey] then
        fontCache[cacheKey] = Font.new(getcustomasset(encodedFontPath), weightEnum)
    end
    
    return fontCache[cacheKey]
end

function Typeface:Get(fontName, weight, style)
    weight = weight or "Regular"
    style = style or "Normal"
    
    local cacheKey = fontName .. "-" .. weight .. "-" .. style
    return fontCache[cacheKey]
end

function Typeface:Init(library)
    if library and library.directory and library.folders then
        ensureDirectories(library.directory, library.folders)
    end
    return self     
end

return Typeface
