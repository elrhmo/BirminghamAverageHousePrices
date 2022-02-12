#Birmingham Average House Prices Test Web map created in R

#setting working directory
setwd("C:/Users/Hp/OneDrive/Documents/R/Projects/Creating a webmap in R")

#Install packages and load libraries
install.packages("leaflet")
install.packages("rgdal")
install.packages("RColorBrewer")

library(leaflet)
library(rgdal)
library(RColorBrewer)

#read and plot Birmingham MSOA file as an object
MSOA <- readOGR("MSOA_BirminghamMeanHousePrices1.geojson")
plot(MSOA)

#setting class bins and color ramp
MSOABins <- c(0, 75000, 150000, 225000, 300000, 3750000, 400000)
MSOAPal <- colorBin("YlOrRd", domain = MSOA$price_paid, bins = MSOABins)

#Hexadecimal colour specification with 5 classes
brewer.pal(n = 5, name = "YlOrRd")

#setting pop-up content
MSOA$popup <- paste("<strong>",MSOA$Birmingham.MSOA_msoa11hclnm,"</strong>", "</br>", 
                    MSOA$GEO_CODE, "</br>",
                    "Mean house price: £", prettyNum(MSOA$price_paid, big.mark = ","))


#creating interactive map using leaflet 
m <- leaflet() %>%
  addProviderTiles(providers$CartoDB.DarkMatter, group = "Basemap - dark") %>%
  addPolygons(data = MSOA,
              stroke = TRUE,
              weight = 0.5,
              color = "#37B1ED",
              smoothFactor = 0.3, #add a smoothing factor
              opacity = 0.9,
              fillColor = ~MSOAPal(price_paid), #apply the colour scheme 
              fillOpacity = 0.8,
              popup = ~popup, #add pop ups
              highlightOptions = highlightOptions(color = "#E2068A", weight = 1.5, #adding hoover effect
                                                  bringToFront = TRUE, fillOpacity = 0.5)) %>%  
  addLegend("bottomright",opacity = 1,
            colors = c("#FFFFB2", "#FECC5C", "#FD8D3C", "#F03B20", "#BD0026"), #setting the colours
            title = "Birmingham Average House Price", #adding a title 
            labels = c("£75,000 - £149,999", "£150,000 - £224,999", "£225,000 - £299,999", "£300,000 - £374,999", ">£375,000")) %>% #adding labels 
  addEasyButton(easyButton( #adding auto zoom to world and current location of user
    icon="fa-globe", title="Zoom to Level 1",
    onClick=JS("function(btn, map){ map.setZoom(1); }"))) %>%
  addEasyButton(easyButton(
    icon="fa-crosshairs", title="Locate Me",
    onClick=JS("function(btn, map){ map.locate({setView: true});}")))

m

  
  
   

  
 








