# Audio_Animatronic_Vocal_Capture
The speech algorithm works by mapping the incoming audio amplitude and converting it into mouth movements

This is a snipped from the main (unpublished code) for the Animatronic Synthia Robot.

void Speech_Algorithm(){
      
  // The speech algorithm works by mapping the incoming audio amplitude and converting it into mouth movements. 
//--------- parameters
 int RAWSound      = 0;                                   // Sound input name
 int FilteredValue = 0;                                   // Created to store value in
 int No_Sound      = 300;                                 // Sets a deadband limit to block out noise and static
 int Mid_Sound     = 425;                                 // mid sound volume to capture
 int Loud_Sound    = 675;                                 // Max sound volume to capture + 
     
     RAWSound = analogRead(SoundIn);                      // Reads the incoming audio
 
 int MappedSound = map(RAWSound, 0,255, 0, 2550 );        // Mapping the incoming sound amplitude

//--------- home position
if(MappedSound < No_Sound)                                // Neutral position of the mouth         
   { MappedSound = 0;}                                   

                                                          // If not at Neutral then increment to Neutral at given rate
if(ValOrbicularis    < 1250){   ValOrbicularis-= 50;}     // The lower the increment the slower it becomes 
if(ValZygomaticus    < 1500){   ValZygomaticus+= 30;}
if(ValDepressorOris  < 1500){ ValDepressorOris+= 30;}
if(ValInferioris     < 1500){    ValInferioris-= 50;}
if(ValMentalis       < 1000){     ValMentalis+= 100;}
if(ValSeptinasi      < 1500){    ValSeptinasi+= 100;}
if(ValTongueServo    < 1400){   ValTongueServo+= 25;}
if(ValJaw            < 1460){           ValJaw+= 20;}
if(ValJawSlew        < 1480){        ValJawSlew+= 5;}
   
if(ValOrbicularis    > 1250){   ValOrbicularis+= 50;}
if(ValZygomaticus    > 1500){   ValZygomaticus-= 30;}
if(ValDepressorOris  > 1500){ ValDepressorOris-= 30;}
if(ValInferioris     > 1500){    ValInferioris+= 50;}
if(ValMentalis       > 1000){     ValMentalis-= 100;}
if(ValSeptinasi      > 1500){    ValSeptinasi-= 100;}
if(ValTongueServo    > 1400){   ValTongueServo-= 25;}
if(ValJaw            > 1475){           ValJaw-= 20;}
if(ValJawSlew        > 1480){        ValJawSlew-= 5;} 
    
//--------- re-mapping
if (MappedSound > 0  )                                                     // If sound is heard
    { FilteredValue = MappedSound =constrain(MappedSound, 300, 2550);;}    // Clone "Mappedsound" to "FilteredValue"         
      FilteredValue -= 15;                                                 // Increment FilteredValue by #
  if (FilteredValue <= 0) {                                                // If no sound is heard               
      FilteredValue = 0;                                                   // Return FilteredValue to zero      

  }  Serial.println(FilteredValue);

//--------- algorithm         
if (MappedSound > No_Sound && MappedSound < Mid_Sound)       // Min position of the mouth                        
   {
   ValOrbicularis     = 1500;                                          
   ValZygomaticus+=     FilteredValue; 
   ValDepressorOris+=   FilteredValue;    
   ValInferioris-=      FilteredValue;  
   ValMentalis        = 800;
   ValSeptinasi-=       FilteredValue; 
   ValTongueServo-=     FilteredValue;  
   ValJaw-=             75; 
   ValJawSlew-=         25;   
   }                                                    

if(MappedSound > Mid_Sound && MappedSound < Loud_Sound)   // Mid position of the mouth  
   {
   ValOrbicularis+=     FilteredValue;                                           
   ValZygomaticus-=     FilteredValue;
   ValDepressorOris+=   FilteredValue;
   ValInferioris+=      FilteredValue;
   ValMentalis+=        FilteredValue;
   ValSeptinasi+=       FilteredValue;
   ValTongueServo+=     FilteredValue;
   ValJaw-=             125;
   ValJawSlew         = 1350;
   }

if (MappedSound > Loud_Sound)                             // Max position of the mouth               
   { 
   ValOrbicularis     = 1100;                                    
   ValZygomaticus+=     FilteredValue;
   ValDepressorOris-=   FilteredValue;
   ValInferioris+=      FilteredValue;
   ValMentalis        = 1500;
   ValSeptinasi+=       FilteredValue;
   ValTongueServo+=     FilteredValue;      
   ValJaw-=             0;
   ValJawSlew+=         FilteredValue;
   } 
     
//--------- constraints                                   // Sets each servo to it own limited constraint
ValOrbicularis   =constrain(ValOrbicularis,   1100, 1500);
ValZygomaticus   =constrain(ValZygomaticus,   1200, 2000); 
ValDepressorOris =constrain(ValDepressorOris,  900, 2000);
ValInferioris    =constrain(ValInferioris,    1400, 1600);
ValMentalis      =constrain(ValMentalis,       800, 1500);
ValSeptinasi     =constrain(ValSeptinasi,     1400, 1600);
ValTongueServo   =constrain(ValTongueServo,   1350, 1600);
ValJaw           =constrain(ValJaw,           1150, 1475);
ValJawSlew       =constrain(ValJawSlew,       1320, 1480);

//--------- Execution
Orbicularis      .write(ValOrbicularis);                 // Writes values to servos
Zygomaticus      .write(ValZygomaticus);    
DepressorOris    .write(ValDepressorOris); 
Inferioris       .write(ValInferioris);     
Mentalis         .write(ValMentalis);      
Septinasi        .write(ValSeptinasi);       
TongueServo      .write(ValTongueServo);     
Jaw              .write(ValJaw);                 
JawSlew          .write(ValJawSlew);  

  delay(30);                                             // Incremental Speed (milliseconds)

}
