```XAML
 <Grid>
     <Grid.ColumnDefinitions>
         <ColumnDefinition/>
         <ColumnDefinition/>
     </Grid.ColumnDefinitions>

    

     <Grid Grid.Column="0">
         <Grid.RowDefinitions>
             <RowDefinition/>
             <RowDefinition/>
         </Grid.RowDefinitions>

         <Button Grid.Column="0" Grid.Row="0" Margin="3" Content="Left"></Button>
         <Button Grid.Column="0" Grid.Row="1" Margin="3" Content="Left"/>
     </Grid>
     
     <Grid Grid.Column="1">
         <Grid.RowDefinitions>
             <RowDefinition/>
             <RowDefinition/>
         </Grid.RowDefinitions>

         <Button Grid.Column="1" Grid.Row="0" Margin="3" Content="Right"/>
         <Button Grid.Column="1" Grid.Row="1" Margin="3" Content="Right"/>

         <GridSplitter Height="10" Grid.Row="1" VerticalAlignment="Top" HorizontalAlignment="Stretch"/>
     </Grid>

    
     
    
     <GridSplitter Grid.Column="0" Grid.Row="0" Grid.RowSpan="2"  Width="10"   />
 </Grid>
```