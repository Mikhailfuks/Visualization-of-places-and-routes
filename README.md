using System;
using System.Collections.Generic;
using System.Windows.Forms;
using GMap.NET;
using GMap.NET.MapProviders;
using GMap.NET.WindowsForms;
using GMap.NET.WindowsForms.Markers;

namespace MapVisualization
{
    public partial class MainForm : Form
    {
        private GMapOverlay markersOverlay;
        private GMapOverlay routesOverlay;

        public MainForm()
        {
            InitializeComponent();
            InitializeMap();
        }

        private void InitializeMap()
        {
            // Initialize GMapControl
            gMapControl1.MapProvider = GMapProviders.GoogleMap;
            gMapControl1.Position = new GMap.NET.PointLatLng(55.7558, 37.6173); // Center on Moscow
            gMapControl1.MinZoom = 2;
            gMapControl1.MaxZoom = 17;
            gMapControl1.Zoom = 10;

            markersOverlay = new GMapOverlay("markers");
            routesOverlay = new GMapOverlay("routes");
            gMapControl1.Overlays.Add(markersOverlay);
            gMapControl1.Overlays.Add(routesOverlay);
        }

        private void buttonAddMarker_Click(object sender, EventArgs e)
        {
            string location = textBoxLocation.Text;

            // Convert location to coordinates
            var point = GMapProviders.GoogleMap.GetPoint(location, out var latLng);
            if (point != null)
            {
                GMarkerGoogle marker = new GMarkerGoogle(latLng, GMarkerGoogleType.green);
                markersOverlay.Markers.Add(marker);
                gMapControl1.Update();
            }
            else
            {
                MessageBox.Show("Location not found.");
            }
        }

        private void buttonDrawRoute_Click(object sender, EventArgs e)
        {
            if (markersOverlay.Markers.Count < 2)
            {
                MessageBox.Show("Please add at least two markers to draw a route.");
                return;
            }

            var routePoints = new List<PointLatLng>();
            foreach (var marker in markersOverlay.Markers)
            {
                routePoints.Add(marker.Position);
            }

            var route = GMap.NET.MapProviders.MapRoute.CreateRoute(routePoints, GMapProviders.GoogleMap);
            foreach (PointLatLng point in route.Points)
            {
                routesOverlay.Routes.Add(new GMapRoute(routePoints, "Route"));
            }

            gMapControl1.Update();
        }
