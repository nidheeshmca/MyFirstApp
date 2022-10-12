namespace Demo
{
    public static class proc
    {
        [System.Runtime.InteropServices.DllImport("USER32.DLL", CharSet = System.Runtime.InteropServices.CharSet.Auto)]
        static extern IntPtr FindWindow(string lpClassName, string lpWindowName);
        [System.Runtime.InteropServices.DllImport("USER32.DLL", CharSet = System.Runtime.InteropServices.CharSet.Auto)]
        static extern bool SetForegroundWindow(IntPtr hWnd);

        [DllImport("user32.dll")]
        static extern bool SetCursorPos(int X, int Y);

        [DllImport("user32.dll")]       

        static extern bool GetCursorPos(ref Point lpPoint);
        public static int currLeft = 200;
        public static int currTop = 400;

        public static void StartProc()
        {
            while (1 == 1)
            {
                var t = Task.Run(() => {
                    Random random = new Random();
                    Task.Delay(random.Next(5000, 30000)).Wait();
                    int max = random.Next(5, 50);
                    int m = random.Next(1, 100);
                    string command = m < 50 ? "{DOWN}" : "{UP}";
                  
                    for (int i = 5; i <= max; i++)
                    {
                        
                      SendKeys.SendWait(command);
                      
                    }
                    SendKeys.Flush();
                });

                var t1 = Task.Run(() => {
                    Random rnd = new Random();
                    Task.Delay(rnd.Next(2000, 25000)).Wait();  
                    LinearSmoothMove(new Point(rnd.Next(200, 800), rnd.Next(200, 600)),100);
                });

             Task.WaitAll(t,t1);
            }
           
        }

        public static void LinearSmoothMove(Point newPosition, int steps)
        {
            Point start = new Point();
            GetCursorPos(ref start);

            PointF iterPoint = start;

            // Find the slope of the line segment defined by start and newPosition
            PointF slope = new PointF(newPosition.X - start.X, newPosition.Y - start.Y);

            // Divide by the number of steps
            slope.X = slope.X / steps;
            slope.Y = slope.Y / steps;

            // Move the mouse to each iterative point.
            for (int i = 0; i < steps; i++)
            {
                iterPoint = new PointF(iterPoint.X + slope.X, iterPoint.Y + slope.Y);
                SetCursorPos(Point.Round(iterPoint).X, Point.Round(iterPoint).Y);
                Thread.Sleep(1);
            }

            // Move the mouse to the final destination.
            SetCursorPos(newPosition.X, newPosition.Y);
        }

    }
}
