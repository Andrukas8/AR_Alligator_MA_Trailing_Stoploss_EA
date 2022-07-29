#include <Trade/Trade.mqh>

int MaPeriod = 8; // MA Period
int MaShift = 5; // MA Shift
input ENUM_TIMEFRAMES TimeFrame = 5; // Time frame
input ENUM_MA_METHOD TslMaMethod = MODE_SMMA; // MA Method
input ENUM_APPLIED_PRICE TslMaAppPrice =  PRICE_CLOSE; // MA Price
input int TslOffsetPoints = 20; // Points above/below the MA
int barsTotal;

int handleMa;

int OnInit()
  {
   handleMa = iMA(_Symbol,TimeFrame,MaPeriod,MaShift,TslMaMethod,TslMaAppPrice);
   return(INIT_SUCCEEDED);
  }

void OnDeinit(const int reason)
  {

   
  }

void OnTick()
  {
  int bars = iBars(_Symbol,TimeFrame);
  int totalPos = PositionsTotal();
       
  if(barsTotal != bars && totalPos > 0) // New bar appeared on the chart
      {  
        barsTotal = bars;
        
        double totalProfit;
        
        string text;
        text += "\n";
        text += " ALLIGATOR TRAILING STOPLOSS EA parameters\n";
        text += " EA TIMEFRAME => " + TimeFrame + "\n";
        text += " MA Period => " + MaPeriod + "\n"; 
        text += " MA Shift => " + MaShift + "\n";
        text += " Offset points => " + TslOffsetPoints + "\n";           
        text += " Positions total => " + totalPos;
        
      
        
        for(int i=0;i<totalPos;i++)
           {                     
            ulong posTicket = PositionGetTicket(i);
                               
            if(PositionSelectByTicket(posTicket))
              {                                  
                     if(PositionGetString(POSITION_SYMBOL) == _Symbol)
                       {          
                        CTrade trade;
                        double posOpenPrice = PositionGetDouble(POSITION_PRICE_OPEN);
                        double posSl = PositionGetDouble(POSITION_SL);
                        double posTp = PositionGetDouble(POSITION_TP);
                        
                        double bid = SymbolInfoDouble(_Symbol,SYMBOL_BID);
                        double ask = SymbolInfoDouble(_Symbol,SYMBOL_ASK);
                        
                        double ma[];
                        CopyBuffer(handleMa,MAIN_LINE,1,MaShift,ma);
         
                        // --------------------
                        // --- BUY POSITION ---
                        // --------------------                        
               
                        if(PositionGetInteger(POSITION_TYPE) == POSITION_TYPE_BUY)
                          {                                               
                           if(ArraySize(ma) > 0)
                             {
                             double sl = NormalizeDouble(ma[MaShift - 1] - TslOffsetPoints * _Point,4);
                             
                             if(sl > posSl && sl < bid)
                               {
                                 if(trade.PositionModify(posTicket,sl,posTp));
                                   {
                                    Print(__FUNCTION__," > Position #",posTicket," was modified by ma tsl.");
                                   }
                               }                     
                              }
                           }               
                                                
                          // ---------------------
                          // --- SELL POSITION ---
                          // ---------------------
                             
                          if(PositionGetInteger(POSITION_TYPE) == POSITION_TYPE_SELL)
                            {                  
                            if(ArraySize(ma) > 0)
                              {                    
                              double sl = NormalizeDouble(ma[MaShift - 1] + TslOffsetPoints * _Point,4);
                              if(sl < posSl || posSl == 0)
                                {                      
                                  if(trade.PositionModify(posTicket,sl,posTp));
                                    {                          
                                     Print(__FUNCTION__," > Position #",posTicket," was modified by ma tsl.");
                                    }
                                }
                            
                           }
                        }              
                    }
                                  
                 }   
           }// for(int i=0;i<totalPos;i++)    
     Comment(text);
  } // if(barsTotal != bars && totalPos > 0)
} // OnTick
