# MonoGame_PortraitorLandscape


using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

using Android.App;
using Android.Content;
using Android.OS;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.Hardware;
using Microsoft.Xna.Framework;
using Microsoft.Devices.Sensors;

namespace Android17
{
    public enum Direction
    {
        left,
        right,
        straight
    }

    public enum Orientation
    {
        unknown,
        portrait,
        landscape
    }

    public class AccelerometerT
    {
        public event ChangeDirectionDlgt ChangeDirectionAcc;
        public event ChangeOrientationHandler ChangeOrientationAcc;
        static SensorManager sensorManager;
        static Sensor sensor;
        SensorListener listener;
       
        bool started = false;
        public Direction TheDirection { get; set; }
        public Orientation TheOrientation { get; set; }

        public AccelerometerT()
        {         
            Initialize();
        }

       

        private void Initialize()
        {
            if (started == false)
            {
             //   if (sensorManager != null && sensor != null)
              //  {
                    sensorManager = (SensorManager)Game.Activity.GetSystemService(Context.SensorService);
                    sensor = sensorManager.GetDefaultSensor(SensorType.Accelerometer);

                listener = new SensorListener();
                sensorManager.RegisterListener(listener, sensor,SensorDelay.Game);
                started = true;
                listener.ChangeDirection += Listener_ChangeDirection;
                listener.ChangeOrientation += Listener_ChangeOrientation;
               
                return;
            }
            else
            {
               // throw new AccelerometerFailedException("Failed to start accelerometer data acquisition. Data acquisition already started.", -1);
            }
        }

        private void Listener_ChangeOrientation(Orientation orientation)
        {
            if (ChangeOrientationAcc != null)
                ChangeOrientationAcc(orientation);
        }

        private void Listener_ChangeDirection(Direction direction)
        {
            if (ChangeDirectionAcc != null)
                ChangeDirectionAcc(direction);
        }

        public delegate void ChangeDirectionDlgt(Direction direction);

        public delegate void ChangeOrientationHandler(Orientation orientation);
        class SensorListener : Java.Lang.Object, ISensorEventListener
        {
            public event ChangeDirectionDlgt ChangeDirection;
            public event ChangeOrientationHandler ChangeOrientation;
            internal Accelerometer accelerometer;
            private Orientation currentOrientation;
            private Orientation previousOrientation;
            float[] history = new float[2];
            String[] direction = { "NONE", "NONE" };
            public Direction theDirection { get; set; }
            public SensorListener()
            {
                history[0] = 0;
                history[1] = 0;
                currentOrientation = Orientation.unknown;
                previousOrientation = Orientation.unknown;
            }
            public void OnAccuracyChanged(Sensor sensor, SensorStatus accuracy)
            {
                //do nothing
            }

            float[] samples = new float[10];
            int counter = 0;
            public void OnSensorChanged(SensorEvent e)
            {
                var values = e.Values;
                samples[counter] = e.Values[0];
                Orientation theOrientation = Orientation.unknown;

                if (values[0] > 5 || values[0] < -5)
                    theOrientation = Orientation.landscape;

                if (values[1] > 5 || values[1] < -5)
                    theOrientation = Orientation.portrait;


                currentOrientation = theOrientation;
                OrientationUpdated(theOrientation);
                //DirectionUpdated(theDirection);
}

            private void DirectionUpdated(Direction theDirection)
            {
                if (ChangeDirection != null)
                    ChangeDirection(theDirection);
            }

            private void OrientationUpdated(Orientation orientation)
            {

                if (currentOrientation != previousOrientation)
                {
                    if (ChangeOrientation != null)
                        ChangeOrientation(orientation);
                }
                previousOrientation= currentOrientation;
            }
        }
    }
}




Daarna in klasse waar je wil weten of het portrait of landscape is:

AccelerometerT acc = new AccelerometerT();
acc.ChangeOrientationAcc += Acc_ChangeOrientationAcc;


private void Acc_ChangeOrientationAcc(Orientation orientation)
        {
            Debug.WriteLine(orientation);
        }

